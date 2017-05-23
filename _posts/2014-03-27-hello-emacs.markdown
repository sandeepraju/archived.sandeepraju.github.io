---
layout: post
title: "Hello Emacs"
date: 2014-03-27 17:41:00
comments: true
categories: [emacs, dev, programming, editor, lisp]
published: true
---
Since the day I found [Sublime Text](http://www.sublimetext.com/), it has been my favorite and default editor for all programming and general writing. The simplicity and the elegance of Sublime is what makes me love it. But sometimes, it is not just enough to know how to use a graphical editor. You need to master a command line based editor as well. I realized this when I moved to Mac at work.

<!-- more -->

I use Mac Book Air for all development at work. The only problem I find with using Mac for development is that, my local development setup and the production setup are way too different. As all the code I write finally run on Linux in production, there might be cases which I might overlook if I run my code on OSx while developing. Considering this fact, I have an [Debian](http://www.debian.org/) server [VirtualBox](https://www.virtualbox.org/) running on my Mac which I use to write code and run tests. This requires me to ssh into my VirtualBox and code on the console. This is when I realized how important mastering a console based editor is. Though I was familiar with [ViM](http://www.vim.org/), I was not at the same pace when I used to use Sublime. Eventually I got fed up and started mounting the VirtualBox's file system over ssh into my Mac and started using Sublime.

All this worked fine for a few months until I found myself in a situation where I had to code on a remote server over ssh for a particular project. Mounting a remote filesystem in this case turned out to be a big disaster due to huge delays in synchronizing files over the network. At this point it was clear that I had to use a console based editor. My first choice was ViM as I was already familiar with it, but I found myself lost very soon with all the complicated shortcuts & modes it had. I am not blaming / judging it. I somewhat felt that ViM was simply not my cup of tea.

I felt I was out of choices. I looked around... Ah! One of my [peer](https://twitter.com/gurteshwarsingh) was using [Emacs](http://www.gnu.org/software/emacs/). I had heard of this editor but never had the time or courage to actually try it out. I saw him seamlessly code in Emacs. It looked fast. It looked like something that I wanted. I installed it.

Spending some time with Emacs, I realized that Emacs is more intuitive. It had this amazing capability that let us configure every single minute possibility to our liking. The key bindings made sense. I started to fiddle around with it and soon ended up making my Emacs look like Sublime ;) This is how my Emacs `init.el` looked like at the end of it...

{% highlight cl %}
;; emacs24 package system
;; from: http://ergoemacs.org/emacs/emacs_package_system.html
(when (>= emacs-major-version 24)
  (require 'package)
  (package-initialize)
  (add-to-list 'package-archives '("melpa" . "http://melpa.milkbox.net/packages/") t)
)

;; load theme
(load-theme 'monokai t)

;; load jedi - python autocomplete
;; https://github.com/tkf/emacs-jedi/issues/37
(add-hook 'python-mode-hook 'jedi:setup)
(setq jedi:complete-on-dot t)
(setq jedi:get-in-function-call-delay 300)

;; don't show splash screen at startup
(setq inhibit-startup-message t)

;; don't show any message in *scratch* buffer
(setq initial-scratch-message "")

;; autopair braces, paranthesis
(autopair-global-mode t)

;; line number config - http://stackoverflow.com/a/8470136/1044366
(global-linum-mode t)
(custom-set-variables '(linum-format 'dynamic))
(defadvice linum-update-window (around linum-dynamic activate)
  (let* ((w (length (number-to-string
		     (count-lines (point-min) (point-max)))))
	 (linum-format (concat "%" (number-to-string w) "d ")))
    ad-do-it))

;; show column number
(setq column-number-mode t)

;; use spaces only
(setq indent-tabs-mode nil)

;; standard indent size
(setq standard-indent 4)

;; enable mouse support
(unless window-system
  (require 'mouse)
  (xterm-mouse-mode t)
  (global-set-key [mouse-4] '(lambda ()
			       (interactive)
			       (scroll-down 1)))
  (global-set-key [mouse-5] '(lambda ()
			       (interactive)
			       (scroll-up 1)))
  (defun track-mouse (e))
  (setq mouse-sel-mode t)
  )

;; don't litter
(setq make-backup-files nil)

;; remove trailing whitespace before save
(add-hook 'before-save-hook 'delete-trailing-whitespace)
{% endhighlight %}

Most of the lisp code is copied from all over the internet. I think eventually I'll learn [Emacs Lisp]() and will be in a better position to understand completely as to what is happening in these configs. I also installed some plugins like jedi (for autocompletion) and autopair.

This is how my Emacs looks finally.

![Screenshot Of My Personalized Emacs]({{ "/img/emacs.png" | prepend: site.baseurl }})

I hope I'll soon master Emacs and make it my primary editor. Waiting to learn Emacs over the coming months!

Happy coding :)
