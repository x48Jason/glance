# Introduction

Glance is a plugin for [Neovim](https://neovim.io) to speed up backporting code review, especially for Gitee.com pull-request workflow.

It is based on some early version of [NeoGit](https://github.com/NeogitOrg/neogit).

## Installation & Configuration

If you are using [Lazy](https://github.com/folke/lazy.nvim) plugin manager, the following spec can be used.

For Gitee usage, you need to generate a private token for Gitee API usage. 

You can omit the gitee part if you are not using for Gitee pull-request workflow.

The ['plenary.nvim'](https://github.com/nvim-lua/plenary.nvim) plugin is a dependency, so it also need to be installed.

```lua
{ "nvim-lua/plenary.nvim" },
{
    "x48Jason/glance",
    opts = {
        gitee = {
            token_file = "~/.token.gitee",
            repo = "openeuler/kernel",
        },
        patchdiff = "diffonly",
        q_quit_log = "off",
    }
}
```

If you are not using Lazy, make sure the plugin is initialized like:

```lua
-- init.lua
local glance = require("glance")
local opts = {
    gitee = {
        token_file = "~/.token.gitee",
        repo = "openeuler/kernel",
    },
    patchdiff = "diffonly",
    q_quit_log = "off",
}
glance.setup(opts)
```

## Usage

### 'Glance prlist' Command

```vim
:Glance prlist [num]
```

Retrieve [num] pull-requests from gitee.com. The pull-requests will be listed in a window.

Press \<enter\> on one pull-requests will fetch the details of this pull-requests from gitee.com and show it in a window.

See following command for how to operate on a pull-request.

### 'Glance pr' Command

```vim
:Glance pr [pr-num]
```

Retrieve detailed information for a pull-request numbered [num] from gitee.com and show it in a window.

The window is devided into 3 parts:
- The top part is the Header part, where the meta data of the pull-requests will be shown.
- The middle part is the Commits part, where all the commits of this pull-requests will be shown.
- The bottom part is the Comments part, where all the comments for this pull-requests will be shown

#### Browse Commit Patch

The following keys are available for showing patches in different ways.

- Press \<enter\> on one commit will show the patch of this commit.
- Press 'l' on one commit will show the upstream commit patch and the backport commit patch, if it is a backport commmit.
- Press 'p' on one commit will show the diff between upstream commit patch and the backport commit patch, if it is a backport commit. Aka, diff of patch.
- Press 'q' on the diff window will quit the diff window.

#### Checkout Commit into Workspace

When in commit-patch (\<enter\> pressed on a commit), \<Ctrl-o\> will checkout the commit into current workspace, and start editing the file corresponding to current diff hunk.

#### Add Comment for Pull-Request

Use \<Ctrl-r\> to add a comment for current pull-request.

#### Delete Comment for Pull-Request

When cursor is in the area of a comment for the pull-request, \<ctrl-d\> will delete the comment.

### 'Glance log' Command

This is for non-Gitee mode. If you fetched changes to local repo, you can directly use this command to browse the commits.

```vim
:Glance log
```
This will read as much as 256 commits from the repo.

If you want to customize the commits that will be read-in, you can append any git-log arguments to 'Glance log', such as:

```vim
:Glance log v6.6..HEAD
```

### 'Glance patchdiff' Command

You can also use 'patchdiff' sub-command to set the display mode for the diff between upstream and backport commit.

If the mode is "diffonly", the diff of commit message will not show.

By default the display mode is "diffonly".

```vim
:Glance patchdiff [full|diffonly]
```

### 'Glance q_quit_log' Command

You can also use 'q_quit_log' sub-command to enable pressing 'q' to quit log view when in it.

By default, it is off

```vim
:Glance q_quit_log [on|off]
```

### Keymaps

In Glance log view, on each commit, following keymaps are available:

| Key       |       Where       | Description                                               |
|-----------|-------------------|-----------------------------------------------------------|
| \<enter\> |   PR Commit Area  | Show the current commit                                   |
|    p      |   PR Commit Area  | Show the diff between upstream commit and backport commit |
|    l      |   PR Commit Area  | Show side by side the upstream commit and backport commit |
|    q      |   Temp Window     | Close the corresponding window                            |
| \<c-o\>   |   Commit-Patch    | Checkout current commit into workspace                    |
| \<c-r\>   |        PR         | Create a comment for current PR                           |
| \<c-d\>   |        PR         | Delete comment under cursor                               |

