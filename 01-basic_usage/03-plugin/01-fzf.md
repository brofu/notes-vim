# fzf


### About 

**`fzf`**

* Official introduction. https://github.com/junegunn/fzf#usage
* Some keywords or `fzf` (by ChatGPT)
    > **Fuzzy Finder**<br> 
    > **Interacitve Filtering**

**`fzf.vim`**

>fzf itself is not a Vim plugin, and the official repository only provides the basic wrapper function for Vim. It's up to the users to write their own Vim commands with it. However, I've learned that many users of fzf are not familiar with Vimscript and are looking for the "default" implementation of the features they can find in the alternative Vim plugins.

### How to Integrate `fzf` to `vim`

**Steps**

1. Install `fzf` plugin (based on `Vundle`)

    ```
    # edit .vimrc 
    Plugin 'junegunn/fzf'
    Plugin 'junegunn/fzf.vim'
    # install them
    :PluginInstall junegunn/fzf junegunn/fzf.vim
    ```
2. Call `fzf` within `vim`
    
    ```
    :FZF
    ```
    if `fzf` is not installed yet, a `no binary found` like error would be returned. To fix this, just install the binary file of `fzf`. Another solution is to configure this in `.vimrc`
    ```
    # .vimrc   
    Plugin 'junegunn/fzf', {'do': { -> fzf#install()}} 
    ```
3. Get familiar with `fzf` plugin (optional)
    ```
    :help fzf
    # mainly focus of fzf:run and fzf:wrap
    # for example,
    # :call fzf#run({'source': 'git ls-files', 'sink': 'e'})
    # and how to define a custom function:
    # command! -bang -complete=dir -nargs=? LS call fzf#run(fzf#wrap({'source': 'ls', 'dir': <q-args>}, <bang>0))
    # -bang
    ```
4. Think about how to support a most common scenarios, with `fzf`
    >* Work within a project
    >* Search all the files under this project, with some text
    >* Preview the context of matched text in the file, similar as `grep keywork -A 5 -B 5`
    
    The first idea is, define a function based on `fzf#run` and `fzf#wrap` to accept the `pattern` and `directory`. That's right. Actually, it turns out such scenarios are already supported by `fzf.vim`. Just, 
    ```
    :help Rg
    # from the manual, we could the definitaion of it
    # `Rg`       |  `fzf#vim#grep(command, [spec dict], [fullscreen bool])`
    ```
    Ok, just try this command 
    ```
    :Rg test
    ```
    Get an error `command failed`, and no file matched at all. Why? Check the source code of `fzf.vim`, and find this
    ```
    \'command! -bang -nargs=* Rg call fzf#vim#grep("rg --column --line-number --no-heading --color=always --smart-case -- ".shellescape(<q-args>), fzf#vim#with_preview(), <bang>0)',
    ```
    Looks like `:Rg` is call `rg` to get the source files. And it turns out `rg` (`ripgrep`) is not installed. Install it
    ```
    brew install ripgrep
    ```
5. Now everything is OK. To make `Rg` command to accept more options, we can update it like this
    ```
    # key points:
    # remove the `--` to accept more options
    # remove the call to `shellescape`
    # use `.` to connect 2 strings
    # with, the `Rg` command cannot work without agrs. That's to say, it must be called like this:
    # `:Rg -w -i Test internal/*` 
    command! -bang -nargs=* Rg call fzf#vim#grep("rg --column --line-number --no-heading --color=always " . <q-args>, fzf#vim#with_preview(), <bang>0)
    ```




**Questions**

* How to grep multiple times (grep pattern_1 | grep pattern_2 | grep pattern_n) as a pipeline with `Rg` or `fzf.vim`?

**References**

* Introductions of `fzf.vim`. https://github.com/junegunn/fzf.vim/blob/master/README.md
* Plugin manual. `:help fzf`
* Source code of `fzf.vim`. https://github.com/junegunn/fzf.vim/blob/master/plugin/fzf.vim#L57








