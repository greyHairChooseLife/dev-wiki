## Subject

warning message might be better (from * 3d7e49c feat(config)!: deprecate highlight groups in config.signs)

## Body

> Please, don't get me wrong if this issue is not written politely enough. I am one of big fan of this project. But I am a newbie in English just like I am in vimmer.

## About what

Last night, I got these warning message after updating plugins.

```
'signs.change.hl' is now deprecated, please define highlight 'GitSignsChange'
'signs.changedelete.hl' is now deprecated, please define highlight 'GitSignsChangedelete'
'signs.delete.hl' is now deprecated, please define highlight 'GitSignsDelete'
```

It breifly says I need to define some highlights. However, defining these one alone didn't resolve the warnings.

It also necessary to remove `hl` key from the plugin configuration:

```
require('gitsigns').setup({
  signs = {
    add          = { hl = 'DevIconQt', text = '+' },
    change       = { hl = 'DevIconGuix', text = '~' },
```

## So what

I guess mentioning `'signs.change.hl'` is clear enough within the context of this plugin or the vim-world. However, for someone like me, the waring could be more helpful.

I suggest adding a more detailed message or example, which would have saved me 15 minutes of confusion, and potentially help others as well.


Thank you for considering this suggestion.

Love,
