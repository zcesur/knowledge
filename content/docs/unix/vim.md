---
Title: Vim
---
## Motion
### Scrolling
- CTRL-u
- CTRL-d

### Screen Repositioning
- zz, zt

### Page Movement
- [H]ome
- [M]iddle
- [L]ast

### Word Movement
- e, b, w, E, B, W

### Text Block Movement
- [[, ]], [], ][
- {{, }}, vip, vap
- %

### Line Movement
- gg, G, 42G, ''

### Character Movement
- f, t, F, T
- ; to repeat

### Other
|                 |                |
|-----------------|----------------|
| `<C-]>`         | `:GoDef`       |
| `<C-LeftMouse>` | `:GoDef`       |
| `<C-t>`         | `:GoDefPop`    |
| `<C-j>`         | `:ALENext`     |
| `<C-k>`         | `:ALEPrevious` |
| `<C-f>`         | `:ALEFix`      |
| `<C-t>`         | `:TableFormat` |

## Windows
- `<C-w>w`

## Miscellaneous
* Insert a file or the result from running an external program into the current buffer:
```vim
:r!openssl rand -hex 32
```

## Links
- [Vim Tab Madness. Buffers vs Tabs](https://joshldavis.com/2014/04/05/vim-tab-madness-buffers-vs-tabs/)
