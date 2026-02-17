# ox_target (Cursor-Based Fork)

> **This is a modified fork of [ox_target](https://github.com/overextended/ox_target) with cursor-based targeting instead of the traditional "eye" icon system.**

If you have any issues or suggestions, feel free to PR, create an issue or join my discord: https://discord.gg/GFZRfCNgkw


## 📖 Preview

https://iili.io/q293YQI.jpg

https://iili.io/q29FjHJ.jpg



## 🎯 What's Different

- **Cursor-based interaction** - Uses a mouse cursor instead of a centered eye icon
- **Click-to-lock system** - Options follow your cursor until you click to lock them in place
- **Green entity outlines** - Only entities with valid targets get outlined (in white)
- **Smart auto-unlock** - Moving cursor to a different target automatically switches focus
- **Cinematic vignette** - Screen edges darken when targeting mode is active
- **Camera lock** - Camera stays fixed while targeting for precise cursor control
- **Self-targeting** - Add target options to your own ped for inventory, emotes, etc.

### How It Works
1. Hold targeting key (default: Left Alt) - cursor appears, vignette effect activates
2. Hover over targets - options appear (faded) and follow your cursor
3. Click to lock - options become solid and clickable (you can press right-click to unlock an option and then target something else)
4. Select an option or right-click to cancel

### Configuration
Add to your `server.cfg` to customize:

```cfg
# Disable entity outline (default: 1 = enabled)
setr ox_target:drawOutline 0

# Use target's interaction distance for outline (default: 1 = enabled)
# When enabled, outline only appears when close enough to interact
setr ox_target:outlineUseTargetDistance 0

# Distance at which entity outline appears (default: 5)
# Only used when outlineUseTargetDistance is disabled
setr ox_target:outlineDistance 10

# Outline color as R,G,B,A (default: 255,255,255,255 = white)
setr ox_target:outlineColor "0,150,255,255"

# Disable vignette/darkened corners effect (default: 1 = enabled)
setr ox_target:vignette 0

# Center cursor on screen when targeting starts (default: 1 = enabled)
setr ox_target:centerCursor 0

# Close target menu after selecting an option (default: 1 = enabled)
# Set to 0 to keep the menu open, allowing multiple option clicks
setr ox_target:closeOnSelect 0
```

---

## 🧍 Self-Target

Target your own ped with custom options - useful for inventory, emotes, status checks, etc.

### Adding Self-Target Options

```lua
exports.ox_target:addSelfTarget({
    {
        name = 'check_inventory',
        icon = 'fa-solid fa-backpack',
        label = 'Check Inventory',
        onSelect = function(data)
            print('Opening inventory...')
            -- data.entity = your ped handle
            -- data.isSelf = true
        end
    },
    {
        name = 'play_emote',
        icon = 'fa-solid fa-face-smile',
        label = 'Emotes',
        onSelect = function(data)
            -- Open emote menu
        end
    }
})
```

### Removing Self-Target Options

```lua
-- Remove a single option by name
exports.ox_target:removeSelfTarget('check_inventory')

-- Remove multiple options
exports.ox_target:removeSelfTarget({ 'check_inventory', 'play_emote' })
```

### How It Works

1. Register self-target options using `addSelfTarget`
2. Enter targeting mode (hold Left Alt)
3. Move your cursor over your own ped/body on screen
4. Your self-target options will appear when hovering over any part of your body
5. Click to interact

### Callback Data

When a self-target option is selected, the callback receives:

| Property | Type | Description |
|----------|------|-------------|
| `entity` | `number` | Your ped handle (`PlayerPedId()`) |
| `coords` | `vector3` | World coordinates where cursor hit |
| `isSelf` | `boolean` | Always `true` for self-targets |

### Conditional Options

You can use `canInteract` to conditionally show/hide self-target options:

```lua
exports.ox_target:addSelfTarget({
    {
        name = 'heal_self',
        icon = 'fa-solid fa-kit-medical',
        label = 'Use Medkit',
        canInteract = function(entity, distance, coords, name)
            -- Only show if player has a medkit item
            return exports.ox_inventory:Search('count', 'medkit') > 0
        end,
        onSelect = function(data)
            -- Use medkit logic
        end
    }
})
```

---

## 🚫 Disabling Targeting (Resource Conflicts)

ox_target uses Left Alt by default and because this fork sets NUI focus to true to show the cursor, it can interfere with other resources that also use this key (e.g., lb-phone camera free-view, cinematic cams, etc.).

To prevent conflicts, other resources can temporarily disable ox_target when they need exclusive use of the Alt key:

### Disable/Enable Targeting

```lua
-- Disable ox_target (e.g., when entering camera free-view mode)
exports.ox_target:disableTargeting(true)

-- Re-enable ox_target (e.g., when exiting camera free-view mode)
exports.ox_target:disableTargeting(false)
```

### Safe Implementation (Optional Check)

If you want to make it optional in case ox_target isn't running:

```lua
local function setTargetDisabled(disabled)
    if GetResourceState('ox_target') == 'started' then
        exports.ox_target:disableTargeting(disabled)
    end
end

-- Usage
setTargetDisabled(true)  -- Disable
setTargetDisabled(false) -- Enable
```

### Check if Targeting is Active

```lua
local isActive = exports.ox_target:isActive()
```

---


## 📚 Documentation

https://overextended.dev/ox_target

## 💾 Download

https://github.com/overextended/ox_target/releases/latest/download/ox_target.zip

