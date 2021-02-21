---
author: david

title: Raw Terminal IO in Scheme

date: 2020-12-23T16:45:32.539-05:00
modified: 2020-12-24T10:18:06.490-05:00
tags:
  - Scheme

  - terminal

  - low-level

---

Recently I've [written](https://yo-dave.com/2020/12/21/getting-the-terminal-window-size-using-scheme/) about doing some fairly low-level operations in Scheme, specifically how to get the window size of a terminal where the program is running. This was part of a little project to write a simple terminal-based text editor in Scheme along the lines of the [kilo](http://antirez.com/news/108) editor by following a tutorial entitled ["Build Your Own Text Editor"](https://viewsourcecode.org/snaptoken/kilo/index.html).

Another part of that project that was surprisingly difficult to accomplish in Scheme was putting the terminal in "raw" mode. Text editors need very fine control of responses to keyboard input. A [question](https://www.reddit.com/r/scheme/comments/ju3g1a/readchar_and_having_to_press_enter/) appeared on Reddit at about the same time that I was looking into the problem. I had posted a similar [question](https://stackoverflow.com/questions/64706126/reading-characters-from-the-terminal-in-raw-mode-using-scheme) to StackOverflow later posted a [solution](https://stackoverflow.com/a/64791919/193509) in Racket. I later went back and created a [gist](https://gist.github.com/clartaq/d7ff89a3fa150e1b281e8b83e8489a8b) for a solution written in Chez Scheme.

The solution for Chez is reproduced here and explained below. It's moderately long (about 250 lines), but broken up into somewhat logical sections.

```scheme
;;
;; direct_tty.ss
;;
;; This is a demonstration program using some of the Chez Scheme FFI to put the
;; terminal into "raw" mode, read a line of characters, possibly containing
;; control characters that would normally terminate input, without echoing
;; the characters typed. Once the user presses the line termination character,
;; (user defined), the program displays the line.
;;
;; The program shows how to obtain character-by-character input with
;; immediate examination of each character. Such a procedure is useful when
;; writing things like text editors where you want the program to respond
;; immediately to keyboard commands without waiting for the entire line
;; to be entered.
;;

;;------------------------------------------------------------------------------
;; Procedures for convenient printing.
;;

;; Return #t if the character is an ASCII control character,
;; false otherwise.
(define (char-control? c)
  (let ((char-num (char->integer c)))
    (or (= char-num 127)
        (< char-num 32))))

;; If c is a control character, it is converted into a two-character
;; string that indicates its special status by preceding it with a "^".
(define (char->readable c)
  (if (char-control? c)
      (string #\^ (integer->char (+ 64 (char->integer c))))
      (string c)))

;; Return a transform of the input string with embedded control chars converted
;; to human-readable form showing a "^" prepended to it.
(define (string->readable s)
  (let loop ((lst (string->list s))
             (acc ""))
    (if (null? lst)
        acc
        (loop (cdr lst) (string-append acc (char->readable (car lst)))))))

;; Display a series of arguments followed by a newline.
(define (println . args)
  (for-each display args)
  (newline))

;; When in raw mode, lines need to be ended with CR/LF pairs to act
;; like normal printing.
(define (println-in-raw . args)
  (for-each display args)
  (display (string #\return #\newline)))

;;------------------------------------------------------------------------------
;; Foreign fuction stuff. Assure we have access to the needed functions from
;; the C runtime and import them.
;;

;; Load the C runtime on macOS. Other OSs will require something different.

(load-shared-object "libc.dylib")

;; Assure that we have access to all of the functions we require.

(if (and (foreign-entry? "isatty")
         (foreign-entry? "tcgetattr")
         (foreign-entry? "tcsetattr")
         (foreign-entry? "cfmakeraw"))
    (println "We have access to needed functions from the C standard library.")
    (println "Can't find needed functions in the C standard library"))

(if (and (foreign-entry? "(cs)s_errno")
         (foreign-entry? "(cs)s_strerror"))
    (println "We have access to needed functions from the Chez C runtime.")
    (begin
      (println "Can't find needed functions in the Chez C runtime.")
      (exit 1)))
;;-----------------------------------------------------------------------------
;; termios-related stuff.

;; Explanation of `termios` and `cfmakeraw`. This is from the `cfmakeraw(3)`
;; Linux man page. Descriptions reflect the result of related flags settings.
;;
;; termios_p->c_iflag &= ~(IGNBRK |  // Do not ignore a BREAK
;;                         BRKINT |  // BREAK will produce a null byte
;;                         PARMRK |  // Ignore parity errors
;;                         ISTRIP |  // Do not strip the eigth bit
;;                         INCLCR |  // Do not translate NL to CR on input
;;                         IGNCR  |  // Do not ignore carriage return on input
;;                         ICRNL  |  // Do not translate a carriage return to newline
;;                         IXON   ); // Disable XON/XOFF flow control on output
;; termios_p->c_oflag &= ~OPOST;     // Disable implementation-defined output processing
;; termios_p->c_lflag &= ~(ECHO   |  // Do not echo characters
;;                         ECHONL |  // Do not echo newline characters
;;                         ICANON |  // Disable canonical mode ("cooked") processing
;;                         ISIG   |  // Do not generate INTR, QUIT, SUSP or DSUSP signals
;;                         IEXTEN ); // Disable implemenation-defined input processing
;; termios_p->c_cflag &= ~(CSIZE  |  // No character size mask
;;                         PARENB ); // Turn off parity generation
;; termios_p->c_cflag |= CS8;        // 8-bit characters
;;
;; NOTE: The use of 8-bit characters makes this a bit incompatible with the `read-char`
;; procedure, which works with UTF-8 characters. Needs more work.
;;
;; The following definitions and structure are from the file
;; /Library/Developer/CommandLineTools/SDKs/MacOSX10.15.sdk/System/Library/Frameworks/Kernel.framework/Headers/sys/termios.h
;; on an iMac.

;; #define NCCS            20
;; typedef unsigned long   tcflag_t;
;; typedef unsigned char   cc_t;
;; typedef unsigned long   speed_t;

(define NCCS 20)

(define-ftype termios
  (struct
   [c_iflag unsigned-long] ; input flags
   [c_oflag unsigned-long] ; output flags
   [c_cflag unsigned-long] ; control flags
   [c_lflag unsigned-long] ; local flags
   ;; It seems like Chez will not let me use the defined constant
   ;; NCCS above for the size of the array. Gotta use the literal 20.
   [c_cc (array 20 char)] ; special control chars
   [c_ispeed unsigned-long] ; input speed
   [c_ospeed unsigned-long] ; output speed
   ))

;;-----------------------------------------------------------------------------
;; Give ourselves access to a bunch of foreign function interfaces.

;; int isatty(int fildes);
;; Returns 1 if the file descriptor represents a terminal, 0 otherwise.
(define isatty (foreign-procedure "isatty" (int) int))

;; int tcgetattr(int fd, struct termios *termios_p);
;; Copy the current attributes into the buffer (termios struct)
;; pointed to. Returns 0 on success, -1 on failure in which case errno
;; will contain the error code.
;(define-c tcgetattr (_fun _int _pointer -> _int))
(define tcgetattr (foreign-procedure "tcgetattr" (int (* termios)) int))

;; int tcsetattr(inf fd, int optional_atcions,
;;               const struct termios *termios_p);
;; Copy the buffer (termios struct) pointed to into the terminal
;; associated the the integer file descriptor. Returns 0 on success,
;; -1 on failure code is copied into errno.
(define tcsetattr (foreign-procedure "tcsetattr" (int int (* termios)) int))

;; void cfmakeraw(struct termios *termio_p);
(define cfmakeraw (foreign-procedure "cfmakeraw" ((* termios)) void))

;; Don't actually use these anymore. Left them here as a reminder about
;; using the "(cs)" calling scheme to reference functions in the
;; Chez Scheme run time.
(define errno (foreign-procedure "(cs)s_errno" () int))
(define strerror (foreign-procedure "(cs)s_strerror" (int) scheme-object))

(if (= 1 (isatty 0))
    (println "We have a terminal.")
    (println "Output is not a terminal."))

;; Allocate a buffer large enough to hold a termios struct
;; and return a pointer to it. Don't forget to release it
;; manually when finished with it.
(define (alloc-termios-buf)
  (make-ftype-pointer termios
                      (foreign-alloc (ftype-sizeof termios))))

;;-----------------------------------------------------------------------------
;; Reading lines from a terminal.

;; Read a line from the current input and return it. This procedure
;; is not much different than the stanard get-line (R5RS) but reads
;; examines individual characters for special actions that can be
;; activated when in raw mode.
(define (inner-read-line)
  (let loop ((running #t)
             (acc "")
             (c (read-char)))
    (cond
     [(or (eof-object? c)
          (char=? c #\q)
          (char=? c #\newline)
          (char=? c #\return)) (begin
                                 (println "Finished  because c = "
                                          (char->readable c))
                                 (set! running #f))]
     [#t (begin
           (set! acc (string-append acc (string c))))])
    (if (not running)
        acc
        (loop #t acc (read-char)))))

;; Return a pointer to a termios structure for the given
;; file descriptor. Assumes the device is already set up
;; in cooked mode.
(define (cooked-termios fd)
  (let ((my-termios (alloc-termios-buf)))
    (tcgetattr fd my-termios)
    my-termios))

;; Return a pointer to a termios structure for the given
;; file descriptor. Assumes the device is already set up
;; in cooked mode and creates a version of the termios
;; structure initialized to set it up in raw mode.
(define (raw-termios fd)
  (let ((my-termios (cooked-termios fd)))
    (cfmakeraw my-termios)
    my-termios))

;; Define some file descriptors for stdin/out. Couldn't find this
;; documented anywhere. These values are from Chez expediter.c.
(define STDIN_FD 0)
(define STDOUT_FD 1)

;; Arguments passed to tcsetattr() describing how to switch to th
;; new termios settings. From termios.h.
(define TCSA 0)      ; Make change immediately.
(define TCSADRAIN 1) ; Drain output, then change.
(define TCSAFLUSH 2) ; Drain output, flush input.

;; Read a line from the standard input in raw mode and return it. The
;; existing terminal attributes are read and restored before exiting.
(define (read-raw-line)
  (let* ((cooked-attrs (cooked-termios STDIN_FD))
         (raw-attrs (raw-termios STDIN_FD)))
    (letrec ((get-raw! (lambda ()
                         (tcsetattr STDIN_FD TCSAFLUSH raw-attrs)))
             (get-cooked! (lambda ()
                            (tcsetattr STDIN_FD TCSAFLUSH cooked-attrs)
                            ;; Don't forget to handle foreign memory.
                            (foreign-free (ftype-pointer-address cooked-attrs))
                            (foreign-free (ftype-pointer-address raw-attrs)))))
      (let ((a-line (dynamic-wind
                      get-raw!
                      inner-read-line
                      get-cooked!)))
        a-line))))

(println "Enter an invisible line of text:")
(println-in-raw "\rResult of reading line in raw mode: "
                (string->readable (read-raw-line)))
```

If you have read the previous post, much of this should look familiar. The first group of procedures are for convenient display of results, used during the development of the solution.

Next comes a section dealing with general FFI functions, loading the C run time library, assuring that expected functions are present, and accessing a couple of functions from the part of the Chez support library written in C.

Following that is a long section of comments documenting the contents of the `termios` structure and declaring the structure for Chez. The fields are not used in this example but I kept the comments to remind me of how things are set up if I ever want to do some bit-fiddling with the flags.

Local versions of some of the support library functions are declared next, ending with a check that the program is actually running in a terminal.

Then we finally arrive at the code that will read the keyboard in "raw" mode. The big advantage of raw mode in a text editor is that the program can respond immediately to individual keystrokes. There is no buffering a line of input and returning it all after the return key is pressed.

The `read-raw-line` procedure is responsible for putting the terminal in raw mode, collecting a line of text, and getting back to normal, sometimes called "cooked" mode. This is all accomplished through the use of the `dynamic-wind` procedure. The first of the three arguments to `dynamic-wind` puts the terminal in raw mode. The second collects the line of text in raw mode. The call to the third procedure assures that the terminal is placed back into "cooked" mode before the program exits.

As mentioned, the actual character collection is done in the `inner-read-line` procedure. Because the terminal is in raw mode, the procedure can examine every character as it is typed and respond immediately. The `cond` can be expanded to perform multiple actions depending on the key pressed. Here, the only special thing it does is watch for the `q` character to terminate input. The keys are collected and clumsily built up into a line of text.

The final few lines in the program will collect the characters for the keys pressed without showing them as they are typed (just for demonstration). When input is terminated, the collected text is displayed. You can also enter control characters that would normally terminate, or at least mess up, input. Control characters in the collected input will be displayed with a leading `^` character.

The handling of getting into raw and cooked modes is not very bulletproof. Those states should probably be constructed explicitly in a real program. But this little demo does not include facilities to set flags within the `termios` structure. That is "left as an exercise for the reader" as they say.

Enjoy!