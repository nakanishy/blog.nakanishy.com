---
title: ターミナル環境 (iTerm, vim, tmux) をフルカラーで使う
date: 2016-08-06 21:50 JST
tags: terminal, neovim, tmux
description: iTerm2, neovim, tmuxをTrue Color (24 bit color) で使うための方法
published: true
---

MacのターミナルやiTermなどターミナル環境において、デフォルトでは16色しか表示することができない。
[iTerm2を256色で使用する](http://qiita.com/muniere/items/e6c5c48a541401ee5af3)方法もあるが、色数としてはまだまだ不十分。
そこで、ターミナル環境においてもTrue Color (24bit color) 約1667万色を使えるようにしてみた、という記録。

[![true color vim](./images/true-color-vim.png 'true color vim')](./images/true-color-vim.png)

## True Colorが表示できる環境

ターミナル環境においてTrue Colorを表示するためには、各ツールがTrue Colorをサポートしている必要がある。
以下、各ツールの対応状況や設定方法について示す。

## 1. Terminal

True Color対応しているターミナルは[TrueColor.md](https://gist.github.com/XVilka/8346728#now-supporting-truecolour)にまとまっている。
iTerm2は、今のところ[Nightly Builds](https://www.iterm2.com/downloads/nightly/#/section/home)でしか対応していない。
Nightly Buildは開発中のビルドであるため、深刻なバグを含んでいる可能性があるため、インストールする際は注意すること。

ターミナルがTrue Colorに対応しているかどうかは、以下のスクリプトで確認できる。
滑らかな虹色が表示されればOK。

<pre class="language-bash"><code>awk 'BEGIN{
    s="/\\/\\/\\/\\/\\"; s=s s s s s s s s;
    for (colnum = 0; colnum<77; colnum++) {
        r = 255-(colnum*255/76);
        g = (colnum*510/76);
        b = (colnum*255/76);
        if (g>255) g = 510-g;
        printf "\033[48;2;%d;%d;%dm", r,g,b;
        printf "\033[38;2;%d;%d;%dm", 255-r,255-g,255-b;
        printf "%s\033[0m", substr(s,colnum+1,1);
    }
    printf "\n";
}'</code>
</pre>

## 2. vim

vim (patch-7.4.1778 以降) または[Neovim](https://github.com/neovim/neovim)がTrue Colorをサポートしている。
NeovimはVimのソースコードをリファクタリングするプロジェクト。今回はせっかくなのでNeovimを導入する。

インストールは[homebrew-neovim](https://github.com/neovim/homebrew-neovim/blob/master/README.md)を参考に。

<pre class="language-bash"><code>$ brew install neovim/neovim/neovim</code></pre>

True Colorを有効するには`.vimrc`または`~/.config/nvim/init.vim`に以下を追加。

<pre class="language-vim"><code>if has('patch-7.4.1778')
  set guicolors
endif
if has('nvim')
  let $NVIM_TUI_ENABLE_TRUE_COLOR=1
endif</code>
</pre>

### Syntax Highlight

当然ながら、使用するSyntax HighlightもTrue Colorサポートしている必要がある。
幾つか挙げておく。

- [mhartington/oceanic-next](https://github.com/mhartington/oceanic-next)
- [joshdick/onedark.vim](https://github.com/joshdick/onedark.vim)
- [whatyouhide/vim-gotham](https://github.com/whatyouhide/vim-gotham)
- [jacoborus/tender.vim](https://github.com/jacoborus/tender.vim)
- [lifepillar/vim-solarized8](https://github.com/lifepillar/vim-solarized8)


## 3. tmux

tmuxはv2.2以降でTrue Colorを使用できる。

<pre class="language-bash"><code>$ tmux -V
tmux 2.2</code></pre>

Homebrewでインストールすると2.2以上が入る。

<pre class="language-bash"><code>$ brew install tmux</code></pre>

## まとめ

やはり綺麗な環境で開発していると気持ちが良い。
見た目の綺麗さに惹かれてAtomに浮気しそうになったが、これでまたVimに落ち着きそう。

