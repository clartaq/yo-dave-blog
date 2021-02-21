---
author: david

title: Getting the Terminal Window Size using Scheme

date: 2020-12-21T17:40:44.401-05:00
modified: 2020-12-23T10:53:35.444-05:00
tags:
  - Scheme

  - terminal

  - low-level

---

Doing some experimenting with the [Scheme](https://en.wikipedia.org/wiki/Scheme_%28programming_language%29) programming language recently has been a lot of fun. It's even simpler in most ways than [Clojure](https://clojure.org).

As part of that experimentation, I've been looking into implementing a simple, terminal-based text editor along the lines of [kilo](https://github.com/antirez/kilo) by following along with the tutorial series ["Build Your Own Text Editor"](https://viewsourcecode.org/snaptoken/kilo/index.html).

Working through those tutorials, one of the first things that smacks you in the face is just how low-level the C programming language is and how very different it feels when compared to Scheme or, really, any other Lisp variant.

And writing a text editor in Scheme is probably not using the best tool for the task. A text editor needs to interact intimately with the keyboard and screen (and maybe the mouse). But it can be done. Look at emacs, a bunch of Lisp wrapped around a small core written in C. Or maybe [Edwin](https://groups.csail.mit.edu/mac/ftpdir/scheme-7.4/doc-html/user_8.html), part of [MIT Scheme](https://www.gnu.org/software/mit-scheme/) is a better example. It is virtually entirely written in Scheme.

So for this little exercise, I'm restricting myself to macOS, because that's all that I have handy, and [Chez Scheme](https://scheme.com), just because I like it.

We will be taking an approach similar to that in the tutorial above, using the multi-purpose [`ioctl`](https://www.ibm.com/support/knowledgecenter/en/SSLTBW_2.3.0/com.ibm.zos.v2r3.bpxbd00/ioctl.htm) function from the underlying C runtime to interrogate and report on the size of the terminal window where the Chez REPL is running. And delving into the C runtime means working with the **FFI** or **F**oreign **F**unction **I**nterface. Every Scheme seems to have one, and it seems every one is different.

So, here is a complete working example. It is explained in detail below.

```lisp
;;; term-size.ss -- Procedure to return the size (in rows and columns)
;;; of the terminal it is running in.

;; Load the C runtime on macOS. Other OSs will require something different.
(load-shared-object "libc.dylib")

;; Define some file descriptors for stdin/out. Couldn't find this
;; documented anywhere. These values are from Chez expediter.c.
(define STDIN_FD 0)
(define STDOUT_FD 1)

(define TIOCGWINSZ #x40087468)

(define-ftype win-size
  (struct
   [ws-row unsigned-short] ; number of rows in the window, in characters
   [ws-col unsigned-short] ; number of columns in the window, in characters
   [ws-xpixel unsigned-short] ; horizontal size of the window, in pixels
   [ws-ypixel unsigned-short] ; vertical size of the window, in pixels
   ))

;; int ioctl(int fildes int cmd, ... /* arg */);
(define ioctl (foreign-procedure "ioctl" (int int (* win-size)) int))

(define errno (foreign-procedure "(cs)s_errno" () int))
(define strerror (foreign-procedure "(cs)s_strerror" (int) scheme-object))

(define (window-size)
  (let* ((win-size-buf (foreign-alloc (ftype-sizeof win-size)))
         (win-size-ptr (make-ftype-pointer win-size win-size-buf)))
    (let ((the-size (dynamic-wind
                      (lambda () #f)
                      (lambda () (let ((ctl-result (ioctl STDOUT_FD TIOCGWINSZ win-size-ptr)))
                                   (if (negative? ctl-result)
                                       (let ((cep (current-error-port)))
                                         (display "Error getting display size.\n" cep)
                                         (display (strerror (errno)) cep)
                                         (newline cep)
                                         (list -1 -1))
                                       (begin
                                         (list (ftype-ref win-size (ws-row) win-size-ptr)
                                               (ftype-ref win-size (ws-col) win-size-ptr))))))
                      (lambda () (foreign-free win-size-buf)))))
      the-size)))

(display "Result of calling window-size: ")
(display (window-size))
(newline)
```

If you want to follow along on the details, I suggest you [get](https://www.embarcadero.com/starthere/xe5/mobdevsetup/ios/en/installing_the_commandline_tools.html) the Apple Developer Command Line Tools and the Chez Scheme [source code](https://github.com/cisco/ChezScheme).

The first thing to do is load the C runtime library into the program. On Mac, that is `libc.dylib`. The libraries to use vary by operating system and CPU architecture. There is an example of loading the correct library for all supported OS and architectures in the Chez Scheme 9.5.4 file `foreign.ms` at about line 2700.

Then the listing `define`s some symbols. `STDIN_FD` and `STDOUT_FD` are the file descriptors for the standard input and standard output. The code assumes that whatever terminal or emulator you are using has the typical values for these special files, 0 and 1, respectively.

The next symbol, `TIOCGWINSZ`, was a bit of a puzzler. The `ioctl` function requires a command code to tell it what you want to do. Since the FFI is no help at importing symbol values, we have to do a little detective work into the header files available as part of the Apple Command Line Tools. `TIOCGWINSZ` is a symbol defined by a series of C macros. The final definition is in `/Library/Developer/CommandLineTools/SDKs/MacOSX11.1.sdk/System/Library/Frameworks/Kernel.framework/Headers/sys/ttycom.h`. In that file, we see that the symbol is defined via another C macro, `_IOR` in the `ioccom.h` header. And that macro is defined in terms of the `_IOC` macro in the same header. Whew! Working through all of those macros yields the value you see in the listing. (I actually cheated and wrote a small C program that just prints the value of the symbol and copied it into the listing. Much less error prone.)

The `win-size` `ftype` is just a transliteration of the `winsize` `struct` from the `ttycom.h` header.

Next comes the import of a few foreign functions. `ioctl` is imported from the C run time. It expects an integer argument for the file descriptor, the command code, and a buffer to put results in. It returns a negative number if an error occurs or 0 on success, usually.

The `errno` and `strerror` functions are imported from the Chez support library. Although these are written in C, they are aware of Scheme and are easier to use because of it. The function names that start with the `(cs)...` seem to flag to the FFI routines that the named function exists in the Chez Scheme support library, though I have never seen it documented anywhere. Use with caution. It may change with newer versions of the software.

Next up comes the `window-size` procedure itself. First a piece of foreign memory is allocated of the size of the `win-size` `ftype` to hold the information returned by `ioctl` call. Next a pointer to that memory is created in a form that can be used by the foreign library.

The next bit of code is not strictly necessary as shown, but since we are using foreign memory, not managed by Scheme, we want to make sure it is freed and returned to the foreign library. `dynamic-wind` is perfect for these types of uses. 
- The first `lambda` is not used in this case. 
- The second `lambda` is where the desired information is retrieved in the call to `ioctl` using the file descriptor, command, and pointer to the buffer to hold the result. If an error occurs, a notice is sent to standard error and a list containing "impossible" row and column counts is returned. If the `ioctl` call is successful, a list containing the number of rows and columns in the terminal display is constructed and returned.
- The third `lambda` is used to make sure that the foreign memory allocated earlier is returned to the system.

The last few lines of the listing call the function and display the results.

Here's an example of running the program a few times in the Chez REPL, changing the size of the terminal window between `load`s. I tried on [iTerm2](https://iterm2.com) (Build 3.4.3) and the Apple Terminal app (version 2.11).

```bash
david@Davids-iMac scheme % chez
Chez Scheme Version 9.5.4
Copyright 1984-2020 Cisco Systems, Inc.

> (load "term-size.ss")
Result of calling window-size: (50 132)
> (load "term-size.ss")
Result of calling window-size: (55 160)
> (load "term-size.ss")
Result of calling window-size: (61 113)
```

Addendum: Part way through writing this, I found the repository [mac-os-termsize](https://github.com/sindresorhus/macos-term-size), a small C language program to do the same thing. Here is the relevant listing from that library.

```c
#include <stdio.h>
#include <string.h>    // strerror
#include <errno.h>     // errno
#include <fcntl.h>     // open(), O_EVTONLY, O_NONBLOCK
#include <unistd.h>    // close()
#include <sys/ioctl.h> // ioctl()

int main() {
	int tty_fd = open("/dev/tty", O_EVTONLY | O_NONBLOCK);
	if (tty_fd == -1) {
		fprintf(stderr, "Opening `/dev/tty` failed (%d): %s\n", errno, strerror(errno));
		return 1;
	}

	struct winsize ws;
	int result = ioctl(tty_fd, TIOCGWINSZ, &ws);
	close(tty_fd);

	if (result == -1) {
		fprintf(stderr, "Getting the size failed (%d): %s\n", errno, strerror(errno));
		return 1;
	}

	fprintf(stdout, "%d\n%d\n", ws.ws_col, ws.ws_row);
	return 0;
}
```

That function takes the additional step of identifying the file descriptor for the terminal it is running on. I haven't found that I needed it, but it is something to keep in mind.