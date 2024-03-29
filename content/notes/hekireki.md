+++
title = "⚡My Emacs Config - 霹靂一閃"
draft = false
+++

Emacsの呼吸壱ノ型, 霹靂一閃.

packageの並び順は [Doom Emacs](https://github.com/hlissner/doom-emacs) の [Molule Index](https://github.com/hlissner/doom-emacs/blob/develop/docs/modules.org) (アルファベット順)に従う.

ref: <https://github.com/tsu-nera/hekireki>

```emacs-lisp
;;; $DOOMDIR/config.el -*- lexical-binding: t; -*-
```


## App {#app}


### Twitter {#twitter}

```emacs-lisp
;; App
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

;; twittering-mode
;; この設定がないと認証が失敗した.
;; twittering-oauth-get-access-token: Failed to retrieve a request token
(add-hook! 'twittering-mode-hook
  (setq twittering-allow-insecure-server-cert t))

```


### eww {#eww}

Emacsのテキストブラウザ([Manual](https://www.gnu.org/software/emacs/manual/html%5Fmono/eww.html))

notes:

-   ewwを起動しただけだとminibufferなのでC-gで消えてしまうので大きくC-x 1とかで大きくする.
-   C-u M-x ewwでひとつのbufferを使いまわすのではなく別のBufferでewwを開く.
-   M-RETでURLを新しいBufferで開く.
-   Doom EmacsだとC-c s o(online)でいろいろと検索できる(w/Chrome). helm-google-suggest的な.

<!--listend-->

```emacs-lisp
(use-package! eww
  :bind
  ("C-c s w" . eww-search-words)
  ("C-c o w" . eww-open-in-new-buffer))
```

-   ace-linkをつかうとewwのlinkをインタラクティブに選択できて便利.

<!--listend-->

```emacs-lisp
(use-package! ace-link
  :config
  (eval-after-load 'eww '(define-key eww-mode-map "f" 'ace-link-eww))
  (ace-link-setup-default))
```


### org-web-tools {#org-web-tools}

ewwとorgを便利にするツール群(<https://github.com/alphapapa/org-web-tools>).

```emacs-lisp
(use-package! org-web-tools
  :bind
  ("C-c i l" . org-web-tools-insert-link-for-url))
```


### Pocket {#pocket}

あとで読むサービス.

```emacs-lisp
(global-set-key (kbd "C-x w p") 'pocket-reader)
(use-package! pocket-reader
  :bind
  ("C-x w l" . pocker-reader-add-link)
  :config
  (setq pocket-reader-open-url-default-function #'eww)
  (setq pocket-reader-pop-to-url-default-function #'eww))
```


### RSS(Elfeed) {#rss--elfeed}

```emacs-lisp
;; elfeed
(global-set-key (kbd "C-x w w") 'elfeed)
(use-package! elfeed
  :config
  (setq elfeed-feeds
        '(
          ("https://yuchrszk.blogspot.com/feeds/posts/default" blog) ; パレオな男
          ("https://www.youtube.com/feeds/videos.xml?channel_id=UCFo4kqllbcQ4nV83WCyraiw" youtube) ; 中田敦彦
          ("https://www.youtube.com/feeds/videos.xml?channel_id=UCFdBehO71GQaIom4WfVeGSw" youtube) ;メンタリストDaiGo
          ("https://www.youtube.com/feeds/videos.xml?playlist_id=PL3N_SB4Wr_S2cGYuI02bdb4UN9XTZRNDu" youtube) ; 与沢の流儀
          ))
  (setq-default elfeed-search-filter "@1-week-ago +unread ")
  (defun elfeed-search-format-date (date)
    (format-time-string "%Y-%m-%d %H:%M" (seconds-to-time date)))
  )
```


## Checkers {#checkers}

```emacs-lisp
;; Checkers
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

```


## Completion {#completion}

```emacs-lisp
;; Completion
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
(use-package! avy
  :bind
  ("M-g c" . avy-goto-char) ;; doom の keybind 上書き.
  ("M-g g" . avy-goto-line) ;; doom の keybind 上書き.
  ("M-g s". avy-goto-word-1))

;; うまく動かないので封印 doom との相性が悪いのかも.
;; ひとまず migemo したいときは isearch で対応.
;; (use-package! avy-migemo
;;  :after migemo
;;  :bind
;;  ("M-g m m" . avy-migemo-mode)
;;  ("M-g c" . avy-migemo-goto-char-timer) ;; doom の keybind 上書き.
;;  :config
;;  (avy-migemo-mode 1)
;;  (setq avy-timeout-seconds nil))

(use-package! swiper
  :bind
;  ("C-s" . swiper) ;; migemo とうまく連携しないので isearch 置き換えを保留. C-c s s で swiper 起動.
  :config
  (ivy-mode 1))

;; avy-migemo-e.g.swiper だけバクる
;; https://github.com/abo-abo/swiper/issues/2249
;;(after! avy-migemo
;;  (require 'avy-migemo-e.g.swiper))

;; org-roam の completion-at-point が動作しないのはこいつかな...
;; (add-hook! 'org-mode-hook (company-mode -1))
;; company はなにげに使いそうだからな，TAB でのみ補完発動させるか.
(setq company-idle-delay nil)
(global-set-key (kbd "TAB") #'company-indent-or-complete-common)
```


### all-the-icons-completion {#all-the-icons-completion}

<https://github.com/iyefrat/all-the-icons-completion>

```emacs-lisp
(use-package! all-the-icons-completion
  :init
  (all-the-icons-completion-mode))
(add-hook! marginalia-mode-hook #'all-the-icons-completion-marginalia-setup)
```


## Config {#config}

```emacs-lisp
;; Config
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; memo:
;; use-package! は:defer, :hook, :commands, or :after が省略されると起動時に load される.
;; after! は package が load されたときに評価される.
;; add-hook! は mode 有効化のとき. setq-hook!は equivalent.
;; どれを使うかの正解はないがすべて use-package!だと起動が遅くなるので
;; 場合によってカスタマイズせよ，とのこと.
;; https://github.com/hlissner/doom-emacs/blob/develop/docs/getting_started.org#configuring-packages
;;
;; doom specific config
;; (setq user-full-name "John Doe"
;;      user-mail-address "john@doe.com")
(setq confirm-kill-emacs nil) ; 終了時の確認はしない.

;; フルスクリーンで Emacs 起動
;; ブラウザと並べて表示することが多くなったのでいったんマスク
;; (add-to-list 'initial-frame-alist '(fullscreen . maximized))

;; This is to use pdf-tools instead of doc-viewer
(use-package! pdf-tools
  :config
  (pdf-tools-install)
  ;; This means that pdfs are fitted to width by default when you open them
  (setq-default pdf-view-display-size 'fit-width)
  :custom
  (pdf-annot-activate-created-annotations t "automatically annotate highlights"))
```


## Editor {#editor}

```emacs-lisp
;; Editor
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; 英数字と日本語の間にスペースをいれる.
(use-package! pangu-spacing
  :config
  (global-pangu-spacing-mode 1)
  ;; 保存時に自動的にスペースを入れるのを抑止.あくまで入力時にしておく.
  (setq pangu-spacing-real-insert-separtor nil))

;; 記号の前後にスペースを入れる.
(use-package! electric-operator)
```


## Emacs {#emacs}

```emacs-lisp
;; Emacs
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
(pixel-scroll-precision-mode)
```


### ace-window {#ace-window}

-   3つ以上のwindowの選択が番号でできる. defaultでC-x oを上書きしてる?
-   C-u C-x o だとwindowをswapできる(ace-swap-window).


## Email {#email}

```emacs-lisp
;; Email
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

```


## Input {#input}

```emacs-lisp
;; Input
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
(set-language-environment "Japanese")
(prefer-coding-system 'utf-8)
(set-default 'buffer-filecoding-system 'utf-8)

;; migemo
(use-package! migemo
  :config
  (setq migemo-command "cmigemo")
  (setq migemo-options '("-q" "--emacs" "-i" "\a"))
  (setq migemo-dictionary "/usr/share/migemo/utf-8/migemo-dict")
  (setq migemo-user-dictionary nil)
  (setq migemo-regex-dictionary nil)
  (setq migemo-coding-system 'utf-8-unix)
  (migemo-init))
```


### fcitx {#fcitx}

```emacs-lisp
(use-package! fcitx
  :config
  (setq fcitx-remote-command "fcitx5-remote")
  (fcitx-aggressive-setup)
  ;; Linux なら t が推奨されるものの、fcitx5 には未対応なためここは nil
  (setq fcitx-use-dbus nil))
```


## Lang {#lang}

```emacs-lisp

```


## OS {#os}

```emacs-lisp
;; OS
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
(require 'exwm-randr)
(setq exwm-randr-workspace-output-plist '(0 "HDMI-1"))
(add-hook 'exwm-randr-screen-change-hook
         (lambda ()
           (start-process-shell-command
            "xrandr" nil "xrandr --output HDMI-1 --primary --right-of eDP-1 --auto")))
(exwm-randr-enable)

(require 'exwm-systemtray)
(exwm-systemtray-enable)

(require 'exwm)
(require 'exwm-config)
(exwm-config-default)
```


## Org mode {#org-mode}

```emacs-lisp
;; Org mode
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; https://github.com/hlissner/doom-emacs/blob/develop/modules/lang/org/README.org
;; https://github.com/tsu-nera/dotfiles/blob/master/.emacs.d/inits/50_org-mode.org

;; スマホとの共有のため, github を clone したものを Dropbox に置いて$HOME に symlink している.
(after! org
  (setq org-directory "~/keido")
  (setq org-default-notes-file "gtd/gtd_projects.org")

  (setq org-return-follows-link t) ;; Enter でリンク先へジャンプ
  (setq org-use-speed-commands t)  ;; bullet にカーソルがあると高速移動
  (setq org-hide-emphasis-markers t) ;; * を消して表示.

  (setq org-footnote-section "Notes") ;; defaultではFootnotesなので変える.
  (setq org-footnote-auto-adjust t)

  ;; M-RET の挙動の調整
  ;; t だと subtree の最終行に heading を挿入, nil だと current point に挿入
  ;; なお，C-RET だと subtree の最終行に挿入され, C-S-RET だと手前に挿入される.
  (setq org-insert-heading-respect-content nil)

  (setq org-startup-indented t)
  (setq org-indent-mode-turns-on-hiding-stars nil)

  (setq org-startup-folded 'show2levels);; 見出しの階層指定
  (setq org-startup-truncated nil) ;; 長い文は折り返す.

  ;; org-babel のソースをキレイに表示.
  (setq org-src-fontify-natively t)
  (setq org-fontify-whole-heading-line t)

  ;; electric-indent は org-mode で誤作動の可能性があることのこと
  ;; たまにいきなり org-mode の tree 構造が壊れるから，とりあえず設定しておく.
  ;; この設定の効果が以下の記事で gif である.
  ;; https://www.philnewton.net/blog/electric-indent-with-org-mode/
  (add-hook! org-mode (electric-indent-local-mode -1))

  ;; org-agenda
  (setq org-refile-targets '((org-agenda-files :maxlevel . 3)))
  (setq org-agenda-time-leading-zero t) ;; 時間表示が 1 桁の時, 0 をつける
  (setq calendar-holidays nil) ;; 祝日を利用しない.
  (setq org-log-done 'time);; 変更時の終了時刻記録.

  ;; スケジュールやデッドラインアイテムは DONE になっていれば表示する
  (setq org-agenda-skip-deadline-if-done nil)
  (setq org-agenda-skip-scheduled-if-done nil)

  (setq org-agenda-include-inactive-timestamps t) ;; default で logbook を表示
  (setq org-agenda-start-with-log-mode t) ;; ;; default で 時間を表示

  ;; org-agenda speedup tips
  ;; https://orgmode.org/worg/agenda-optimization.html

  ;; 何でもかんでも agenda すると思いので厳選.
  (setq org-agenda-files '("~/Dropbox/keido/notes/gtd/gtd_projects.org"
                           "~/Dropbox/keido/notes/journals/journal.org"
                           ;; projectsディレクトリにある.orgをみる.
                           ;; その配下のorgファイルは対象にはならない.
                           "~/Dropbox/keido/notes/gtd/projects"))

  ;; 期間を限定
  (setq org-agenda-span 7)
                                        ; Inhibit the dimming of blocked tasks:
  (setq org-agenda-dim-blocked-tasks nil)
  ;; Inhibit agenda files startup options:
  (setq org-agenda-inhibit-startup nil)
  ;; Disable tag inheritance in agenda:
  (setq org-agenda-use-tag-inheritance nil)

  ;; org-capture
  ;; https://orgmode.org/manual/Capture-templates.html
  (defun my/create-timestamped-org-file (path)
    (expand-file-name (format "%s.org" (format-time-string "%Y%m%d%H%M%S")) path))
  (defun my/create-date-org-file (path)
    (expand-file-name (format "%s.org" (format-time-string "%Y-%m-%d")) path))

  (defconst my/captured-notes-file "~/keido/inbox/inbox.org")

  (setq org-capture-templates
        '(("i" "📥 Inbox" entry
           (file "~/keido/inbox/inbox.org") "* %?\nCaptured On: %U\n"
           :klll-buffer t)
          ("I" "📥+🌐 Inbox+Browser" entry
           (file "~/keido/inbox/inbox.org")
           "* %?\nSource: [[%:link][%:description]]\nCaptured On: %U\n"
           :klll-buffer t)
          ("q" "📥+🌐 Inbox+Browser(quote)" entry
           (file "~/keido/inbox/inbox.org")
           "* %?\nSource: [[%:link][%:description]]\nCaptured On: %U\n%i\n"
           :klll-buffer t)
          ("c" "☑ Planning" plain
           (file+headline (lambda () (my/create-date-org-file "~/keido/notes/journals/daily"))
                          "Planning")
           "%?"
           :unnarrowed t
           :kill-buffer t)
          ("t" "🤔 Thought" entry
           (file+headline (lambda () (my/create-date-org-file "~/keido/notes/journals/daily"))
                          "Thoughts")
           "* 🤔 %?\n%T"
           :empty-lines 1
           :unnarrowed t
           :kill-buffer t)
          ("T" "🤔+📃 Thought+Ref" entry
           (file+headline (lambda () (my/create-date-org-file "~/keido/notes/journals/daily"))
                          "Thoughts")
           "* 🤔 %?\n%T from %a\n"
           :empty-lines 1
           :unnarrowed t
           :kill-buffer t)
          ("l" "🤔+🌐 Thought+Browser" entry
           (file+headline (lambda () (my/create-date-org-file "~/keido/notes/journals/daily"))
                          "Thoughts")
             "* 🤔 %?\n%T from [[%:link][%:description]]\n"
           :empty-lines 1
           :unnarrowed t
           :kill-buffer t)
          ("p" "🍅 Pomodoro" entry
           (file+headline (lambda () (my/create-date-org-file "~/keido/notes/journals/daily"))
                          "Pomodoros")
           "* 🍅 %?\n%T"
           :empty-lines 1
           :unnarrowed t
           :kill-buffer t)
          ("j" "🖊 Journal" plain
           (file (lambda () (my/create-date-org-file "~/keido/notes/journals/daily")))
           "%?"
           :empty-lines 1
           :unnarrowed t
           :kill-buffer t)
          ("J" "🖊+📃 Journal+Ref" plain
           (file (lambda () (my/create-date-org-file "~/keido/notes/journals/daily")))
           "%?\n%a"
           :empty-lines 1
           :unnarrowed t
           :kill-buffer t)
          ("L" "🖊+🌐 Journal+Browser" plain
           (file (lambda () (my/create-date-org-file "~/keido/notes/journals/daily")))
             "%?\nSource: [[%:link][%:description]]\nCaptured On: %U\n"
           :empty-lines 1
           :unnarrowed t
           :kill-buffer t)
          ("z" "🎓 Zettelkasten" plain
           (file (lambda () (my/create-timestamped-org-file "~/keido/notes/zk")))
           "#+TITLE:🎓%?\n")
          ("w" "📝 Wiki" plain
           (file (lambda () (my/create-timestamped-org-file "~/keido/notes/wiki")))
           "#+EXPORT_FILE_NAME: ~/repo/futurismo4/wiki/xxx.rst
#+OPTIONS: toc:t num:nil todo:nil pri:nil ^:nil author:nil *:t prop:nil
#+TITLE:📝%?\n")
          ))

  ;; org-babel
  ;; 評価でいちいち質問されないように.
  (setq org-confirm-babel-evaluate nil)
  ;; org-babel で 実行した言語を書く. デフォルトでは emacs-lisp だけ.
  (org-babel-do-load-languages
   'org-babel-load-languages
   '((lisp . t)
     (shell . t)))
  )

;; org-mode で timestamp のみを挿入するカスタム関数(hh:mm)
(after! org
  (defun my/insert-timestamp ()
    "Insert time stamp."
    (interactive)
    (insert (format-time-string "%H:%M")))
  (map! :map org-mode-map "C-c C-." #'my/insert-timestamp))

;; +pretty(org-superstar-mode)関連
;;; Titles and Sections
;; hide #+TITLE:
;; (setq org-hidden-keywords '(title))
;; set basic title font
;; (set-face-attribute 'org-level-8 nil :weight 'bold :inherit 'default)
;; Low levels are unimportant => no scaling
;; (set-face-attribute 'org-level-7 nil :inherit 'org-level-8)
;; (set-face-attribute 'org-level-6 nil :inherit 'org-level-8)
;; (set-face-attribute 'org-level-5 nil :inherit 'org-level-8)
;; (set-face-attribute 'org-level-4 nil :inherit 'org-level-8)
;; Top ones get scaled the same as in LaTeX (\large, \Large, \LARGE)
;; (set-face-attribute 'org-level-3 nil :inherit 'org-level-8 :height 1.2) ;\large
;; (set-face-attribute 'org-level-2 nil :inherit 'org-level-8 :height 1.44) ;\Large
;; (set-face-attribute 'org-level-1 nil :inherit 'org-level-8 :height 1.728) ;\LARGE
;; Only use the first 4 styles and do not cycle.
(setq org-cycle-level-faces nil)
(setq org-n-level-faces 4)
;; Document Title, (\huge)
;; (set-face-attribute 'org-document-title nil
;;                    :height 2.074
;;                    :foreground 'unspecified
;;                    :inherit 'org-level-8)

;; (with-eval-after-load 'org-superstar
;;  (set-face-attribute 'org-superstar-item nil :height 1.2)
;;  (set-face-attribute 'org-superstar-header-bullet nil :height 1.2)
;;  (set-face-attribute 'org-superstar-leading nil :height 1.3))
;; Set different bullets, with one getting a terminal fallback.
(setq org-superstar-headline-bullets-list '("■" "◆" "●" "▷"))
;; (setq org-superstar-special-todo-items t)

;; Stop cycling bullets to emphasize hierarchy of headlines.
(setq org-superstar-cycle-headline-bullets nil)
;; Hide away leading stars on terminal.
;; (setq org-superstar-leading-fallback ?\s)
(setq inhibit-compacting-font-caches t)

;; 読書のためのマーカー（仮）
;; あとでちゃんと検討と朝鮮しよう.
;; (setq org-emphasis-alist
;;   '(("*" bold)
;;     ("/" italic)
;;     ("_" underline))
;;     ("=" (:background "red" :foreground "white")) ;; 書き手の主張
;;     ("~" (:background "blue" :foreground "white")) cddddd;; 根拠
;;     ("+" (:background "green" :foreground "black")))) ;; 自分の考え

```


### org-toggl {#org-toggl}

org-modeをTogglと連携させる.
<https://github.com/mbork/org-toggl>

```emacs-lisp
(use-package! org-toggl
  :after org
  :config
  (setq org-toggl-inherit-toggl-properties t)
  (toggl-get-projects)
  (org-toggl-integration-mode))
```


### ox-hugo {#ox-hugo}

Org-modeで書いたブログ記事をHugoにあったMarkdown形式に変換する.

ブログFuturismoはOrg-modeで執筆してこれを利用してMarkdownに変換している.

```emacs-lisp
(use-package! ox-hugo
  :after 'ox)
```


### ox-rst {#ox-rst}

Org-modeで書いたWiki用のページをSphinxで公開するためにreST形式に変換する.

リンク形式がうまく変換できないのでけっこう強引に変換している(もう少しうまく改善したい).

```emacs-lisp
(use-package! ox-rst
  :after 'ox)

(after! ox
  (defun my/rst-to-sphinx-link-format (text backend info)
    (when (and (org-export-derived-backend-p backend 'rst) (not (search "<http" text)))
      (replace-regexp-in-string "\\(\\.org>`_\\)" ">`" (concat ":doc:" text) nil nil 1)))
  (add-to-list 'org-export-filter-link-functions
               'my/rst-to-sphinx-link-format))
```


### org-journal {#org-journal}

<https://github.com/bastibe/org-journal>

```emacs-lisp
(use-package! org-journal
  :after org
  :bind
  ("C-c r d n" . org-journal-new-entry)
  ("C-c r d d" . org-journal-open-current-journal-file)
  :custom
  (org-journal-date-prefix "#+TITLE: ✍")
  (org-journal-file-format "%Y-%m-%d.org")
  (org-journal-dir (file-truename "~/keido/notes/journals/daily"))
  (org-journal-date-format "%Y-%m-%d")
  :config
  (setq org-journal-enable-agenda-integration t)
  (defun org-journal-file-header-func (time)
     "Custom function to create journal header."
     (concat
      (pcase org-journal-file-type
        (`daily "#+STARTUP: showeverything"))))
  ;;     ;; (`weekly "#+TITLE: Weekly Journal\n#+STARTUP: folded")
  ;;     ;;(`monthly "#+TITLE: Monthly Journal\n#+STARTUP: folded")
  ;;     ;; (`yearly "#+TITLE: Yearly Journal\n#+STARTUP: folded"))))
  (setq org-journal-file-header 'org-journal-file-header-func)

  ;; org-roamに対応させるためにorg-idを生成
  (defun org-create-new-id-journal ()
    (goto-char (point-min))
    (org-id-get-create)
    (goto-char (point-max)))
  (add-hook 'org-journal-after-header-create-hook 'org-create-new-id-journal)
)
```


### Org-roam {#org-roam}

Zettelkasten MethodのOrg-roam実装.

org-roam-dialiesよりもorg-journalを利用する(org-agendaの都合).

```emacs-lisp
;; org-roam
(setq org-roam-directory (file-truename "~/keido/notes"))
(setq org-roam-db-location (file-truename "~/keido/db/org-roam.db"))

(use-package! org-roam
  :after org
  :init
  (setq org-roam-v2-ack t)
  (map!
        :leader
        :prefix ("r" . "org-roam")
        "f" #'org-roam-node-find
        "i" #'org-roam-node-insert
        "l" #'org-roam-buffer-toggle
        "t" #'org-roam-tag-add
        "T" #'org-roam-tag-remove
        "a" #'org-roam-alias-add
        "A" #'org-roam-alias-remove
        "r" #'org-roam-ref-add
        "R" #'org-roam-ref-remove
        "o" #'org-id-get-create
        "u" #'my/org-roam-update
        )
  :custom
  ;; ファイル名を ID にする.
  (org-roam-capture-templates
   '(("d" "default" plain "%?"
      :target (file+head "%<%Y%m%d%H%M%S>.org"
                         "#+title: ${title}\n")
      :unnarrowed t)
     ("z" "🎓 Zettelkasten" plain "%?"
      :target (file+head "zk/%<%Y%m%d%H%M%S>.org"
                         "#+title:🎓${title}\n")
      :unnarrowed t)
     ("w" "📝 Wiki" plain "%?"
      :target (file+head "zk/%<%Y%m%d%H%M%S>.org"
                         "#+title:📝${title}\n")
      :unnarrowed t)
     ("f" "🦊 Darkfox" plain "%?"
      :target (file+head "darkfox/%<%Y%m%d%H%M%S>.org"
                         "#+title:🦊${title}\n")
      :unnarrowed t)
     ("b" "📚 Book" plain
      "%?

- title: %^{title}
- authors: %^{author}
- date: %^{date}
- publisher: %^{publisher}
- url: http://www.amazon.co.jp/dp/%^{isbn}
"
      :target (file+head "zk/%<%Y%m%d%H%M%S>.org"
                         "#+title:📚${title} - ${author}(${date})\n")
      :unnarrowed t)
     ("t" "🎤 Talk" plain
      "%?

- title: %^{title}
- editor: %^{editor}
- date: %^{date}
- url: %^{url}
"
      :target (file+head "zk/%<%Y%m%d%H%M%S>.org"
                         "#+title:🎤${title} - ${editor}(${date})\n")
      :unnarrowed t)
     ("o" "💻 Online" plain
      "%?

- title: %^{title}
- authors: %^{author}
- url: %^{url}
"
      :target (file+head "zk/%<%Y%m%d%H%M%S>.org"
                         "#+title:💻${title}\n")
      :unnarrowed t)))
  (org-roam-extract-new-file-path "%<%Y%m%d%H%M%S>.org")
  ;;        :map org-mode-map
  ;;        ("C-M-i"    . completion-at-point)
  :config
  (defun my/org-roam-update ()
    (interactive)
    (org-roam-update-org-id-locations)
    (org-roam-db-sync))

  (setq +org-roam-open-buffer-on-find-file nil)
  (org-roam-db-autosync-mode))


(use-package! websocket
    :after org-roam)
(use-package! org-roam-ui
    :after org-roam ;; or :after org
;;         normally we'd recommend hooking orui after org-roam, but since org-roam does not have
;;         a hookable mode anymore, you're advised to pick something yourself
;;         if you don't care about startup time, use
    ;; :hook (after-init . org-roam-ui-mode)
    :config
    (setq org-roam-ui-sync-theme t
          org-roam-ui-follow t
          org-roam-ui-update-on-save t
          org-roam-ui-open-on-start t))

(use-package! org-roam-timestamps
   :after org-roam
   :config
   (org-roam-timestamps-mode)
   (setq org-roam-timestamps-remember-timestamps nil)
   (setq org-roam-timestamps-remember-timestamps nil))


;; 今どきのアウトライナー的な線を出す.
;; Terminal Mode ではつかえないので一旦無効化する.
;; (require 'org-bars)
;; (add-hook! 'org-mode-hook #'org-bars-mode)

;; 空白が保存時に削除されると bullet 表示がおかしくなる.
;; なお wl-bulter は doom emacs のデフォルトで組み込まれている.
(add-hook! 'org-mode-hook (ws-butler-mode -1))
```


#### Org-roam管理下のノートの全文検索 {#org-roam管理下のノートの全文検索}

[deft](https://jblevins.org/projects/deft/) より高速(<https://org-roam.discourse.group/t/using-consult-ripgrep-with-org-roam-for-searching-notes>).

```emacs-lisp
(defun my/org-roam-rg-search ()
  "Search org-roam directory using consult-ripgrep. With live-preview."
  (interactive)
  (counsel-rg nil org-roam-directory))
(global-set-key (kbd "C-c r s") 'my/org-roam-rg-search)
```


#### org-publish(Org-roamのノートをサイトへ公開) {#org-publish--org-roamのノートをサイトへ公開}

```emacs-lisp
(setq org-publish-project-alist
      (list
       (list "keido"
             :recursive t
             :base-directory (file-truename "~/keido/notes/wiki")
             :publishing-directory "~/repo/keido-hugo/content/notes"
             :publishing-function 'org-hugo-export-wim-to-md)))
```


### bibtex関連(Org-ref) {#bibtex関連--org-ref}

文献管理. Zoteroと連携して，論文というよりは書籍やYoutube動画やWeb記事のメモに利用.

-   org-ref
-   ivy-bibtex
    -   ivyのactionは ivy-bibtexでC-SPCで選択-> C-M-oでaction選択候補を出し，pとかeとか押す.
-   org-roam-bibtex

<!--listend-->

```emacs-lisp
(use-package! org-ref
  :config
  (setq bibtex-completion-bibliography (list (file-truename "~/keido/references/zotLib.bib")))

  (setq bibtex-completion-additional-search-fields '(keywords))
  (setq bibtex-completion-display-formats
    '((online       . "${=has-pdf=:1}${=has-note=:1} ${=type=:6} ${year:4} ${author:24} ${title:*}")
      (book         . "${=has-pdf=:1}${=has-note=:1} ${=type=:6} ${year:4} ${author:24} ${title:*}")
      (video        . "${=has-pdf=:1}${=has-note=:1} ${=type=:6} ${year:4} ${editor:24} ${title:*}")
      (paper        . "${=has-pdf=:1}${=has-note=:1} ${=type=:6} ${year:4} ${author:24} ${title:*}")
      (t            . "${=has-pdf=:1}${=has-note=:1} ${=type=:6} ${year:4} ${author:24} ${title:*}")))
  (setq bibtex-completion-pdf-symbol "📓")
  (setq bibtex-completion-notes-symbol "📝")

  (setq bibtex-completion-pdf-field "file")
  ;; (setq bibtex-completion-pdf-open-function
  ;;	(lambda (fpath)
  ;;	  (call-process "open" nil 0 nil fpath)))

  ;; Create fields for Film type
  (add-to-list 'bibtex-biblatex-field-alist
               '(("video" "Video or Audio(like YouTube)")))

  (add-to-list 'bibtex-biblatex-entry-alist
               '("video" "A Video"
                 ("video", "title" "editor" "date" "url" "urldate" "abstract" "editortype")
                 nil
                 "keywords"))
  (bibtex-set-dialect 'biblatex))

(use-package! ivy-bibtex
  :after org-ref
  :init
  (map!
   :leader
   :prefix ("b" . "org-ref")
     "b" #'org-ref-bibtex-hydra/body
     "v" #'ivy-bibtex
     "c" #'org-ref-insert-cite-link
     "a" #'orb-note-actions
     "i" #'orb-insert-link)
  :config
  (setq ivy-re-builders-alist
        '((ivy-bibtex . ivy--regex-ignore-order)
          (t . ivy--regex-plus)))
  (setq ivy-bibtex-default-action #'ivy-bibtex-open-url-or-doi)
  (ivy-set-actions
   'ivy-bibtex
   '(("p" ivy-bibtex-open-any "Open PDF, URL, or DOI" ivy-bibtex-open-any)
     ("e" ivy-bibtex-edit-notes "Edit notes" ivy-bibtex-edit-notes)))
  )

(use-package! org-roam-protocol
  :after org-protocol)

(use-package! org-roam-bibtex
  :after org-roam ivy-bibtex
  :hook (org-mode . org-roam-bibtex-mode)
  :custom
  (orb-insert-interface 'ivy-bibtex)
  :config
    (setq orb-preformat-keywords '("author" "date" "url" "title" "isbn" "publisher" "urldate" "editor" "file"))
    (setq orb-process-file-keyword t)
    (setq orb-attached-file-extensions '("pdf")))
```


### Org-noter {#org-noter}

PDFの注釈を管理する. [:link:weirdNox/org-noter](https://github.com/weirdNox/org-noter)

はじめの起動がどうやればいいのかワカラなかった.
特定のファイルに記録を残したい場合はPDFのBufferではなく,
適当なheading作成してM-x org-noterを起動するとPDFを選択できる.

M-x org-noter-create-skeltonという関数がヤばい. [🔗Youtube動画(1:08)](https://youtu.be/lCc3UoQku-E?t=68)
PDFからOutlineを抜き出してOrg fileに生成して，あとはそのOrg-fileのBulletのカーソルを移動するとPDFのほうもシンクロして移動できる.

凄すぎて笑った😂

```emacs-lisp
(use-package! org-noter
  :after (:any org pdf-view)
  :config
  (setq
   ;; I want to see the whole file
   org-noter-hide-other nil
   ;; Everything is relative to the main notes file
   org-noter-notes-search-path (list (file-truename "~/keido/notes/wiki"))
   ))
```


## Term {#term}

```emacs-lisp
;; Term
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
```


## Tools {#tools}

```emacs-lisp
;; Tools
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
```


## UI {#ui}


### Doom {#doom}

```emacs-lisp
;; UI
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; どうもフォントが奇数だと org-table の表示が崩れる.
;; Source Han Code JP だとそもそも org-table の表示が崩れる.
;; terminal だと大丈夫な模様.そもそも Terminal はこの設定ではなくて Terminal Emulator の設定がきく.

;; (setq doom-font (font-spec :family "Source Han Code JP" :size 12 ))
(setq doom-font (font-spec :family "Ricty Diminished" :size 15))
;; doom-molokaiやdoom-monokai-classicだとewwの表示がいまいち.
(setq doom-theme 'doom-monokai-pro)
(doom-themes-org-config)

;; counselとdoom-modelineが相性悪いようなのでworkspace name表示のためには追加で設定.
;; https://github.com/hlissner/doom-emacs/issues/314
(after! doom-modeline
  (setq doom-modeline-persp-name t))
```


### emojify {#emojify}

Emacsで絵文字をつかう.

どうもemojifyの絵文字辞書は，emojione-v2.2.6-22というものでやや古い.
Twitterが好きなのでTwitterのオープンソース辞書のtwemojiに変更.

<https://github.com/iqbalansari/emacs-emojify/blob/master/data/emoji-sets.json>

```emacs-lisp
(after! emojify
  (setq emojify-emoji-set "twemoji-v2-22"))
```

ただ，2022現在twemojiはv13なのでv2は古いな..というかでないやつもおおい.

Emacsの機能でemoji-searchがあるのでこれも設定しておこう.
こっちの辞書のほうが扱える文字か多い.

```emacs-lisp
;; doomだと C-c i eでemojify-insert-emoji
(global-set-key (kbd "C-c i E") 'emoji-search)
```


### perfect-margin {#perfect-margin}

いい感じにmarginをとってくれる (<https://github.com/mpwang/perfect-margin>)

```emacs-lisp
(use-package! perfect-margin
  :config
  (perfect-margin-mode 1))
```


### Others {#others}

```emacs-lisp

(setq display-line-numbers-type t) ; 行番号表示

;; less でのファイル閲覧に操作性を似せる mode.
;; view-mode は emacs 内蔵. C-x C-r で read-only-mode でファイルオープン
;; doom emacs だと C-c t r で read-only-mode が起動する.
(add-hook! view-mode
  (setq view-read-only t)
  (define-key ctl-x-map "\C-q" 'view-mode) ;; assinged C-x C-q.

  ;; less っぼく.
  (define-key view-mode-map (kbd "p") 'view-scroll-line-backward)
  (define-key view-mode-map (kbd "n") 'view-scroll-line-forward)
  ;; default の e でもいいけど，mule 時代に v に bind されてたので, emacs でも v に bind しておく.
  (define-key view-mode-map (kbd "v") 'read-only-mode))

```
