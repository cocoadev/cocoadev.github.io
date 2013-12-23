---
layout: page
title: UsingEmacsForCocoaDev
---

I prefer to use Emacs for Cocoa Development.  Here's some elisp I use.  Please add your own as you wish:

-- General/JivaDeVoe

----

Swap between Objective-C++ .mm file and it's header and implementation and test units:

    
(defun swap-header-and-impl ()
  (interactive)

  (if (equal ".mm" (substring buffer-file-truename (- (length buffer-file-truename) 3)))
      (find-file (replace-regexp-in-string "\\.mm" "\.h" buffer-file-truename))
    (find-file (replace-regexp-in-string "\\.h" "\.mm" buffer-file-truename)))

)

(defun swap-impl-and-test ()
  (interactive)

  (if (equal "Test" (substring buffer-file-truename -6 -2))
      (find-file (replace-regexp-in-string "Test\\." "\." buffer-file-truename))
    (find-file (replace-regexp-in-string "\\." "Test\." buffer-file-truename)))
)


I like to bind these to F11 and F12 respectively, so I can quickly swap back and forth between things very quickly and easily.

----

I like to make my *.h files be in Obj-C mode so that it'll recognize //-style comments. 
    
(add-to-list 'auto-mode-alist '("\\.h" . objc-mode))

does the trick

----

Also see [http://borkware.com/rants/emacs-dev/]
