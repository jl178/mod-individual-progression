# Individual Player Progression

## Azerothcore Playerbots Module

This module simulates progress through expansions and expansion tiers for individual players.

Each tier is designed to simulate the experience of being within that tier and expansion, within the limitations of the WotLK client. This means Vanilla content is like it was during Vanilla WoW.

The goal of the module is to focus on the journey of the player. All catch-up mechanics have been removed. There is no goal to rush to end-game or find a "fresh" server, because each new character is a fresh experience.

Note that this module either requires many players working together, or adjustments for smaller raid size to allow individual groups to progress. This module works well together with Playerbots and NPCbots.

Lower-level content added in later expansions that is not "catch-up" content has NOT been removed. This includes Draenei and Blood Elf starting zones, Inscription and Jewelcrafting skills, new quests and hubs added to the Vanilla world in TBC, and so on. I feel this content does not negatively impact the player journey like "catch-up" content. Some of this content can optionally be phased until the appropriate patch in which it was introduced for those who prefer an even more authentic Vanilla world - see the optional SQL files for more details.

## Some Included Features
* NPCs and Game Objects are aware of individual player progress, visible depending on player progression
* Restoration of content that was removed, nerfed or released in a later patch. (examples: original Naxxramas, Onyxia, TBC Attunement quests and open world elites)
* Overhaul of vanilla creatures and quests. (Smart AI, quest drop chances, creature respawn times, patrols and spawn locations have all been restored.)
* Optional power and healing adjustment for Vanilla and TBC content to simulate the original world difficulty.
* WotLK emblems progression.
* Original vanilla AV
* Isle of Quel'Danas Progression
* Playerbots support

Here you can find a list of all the [changes](https://github.com/ZhengPeiRu21/mod-individual-progression/wiki/List-of-Changes) and [progression tiers](https://github.com/ZhengPeiRu21/mod-individual-progression/wiki/List-of-Progression-Tiers).

Options are provided that allow changes to the player experience, based on preference. Please see the config file.

## How to Install
Please see [How to Install](https://github.com/ZhengPeiRu21/mod-individual-progression/wiki/How-to-Install)

- You need my fork of AzerothCore for this module! https://github.com/Grimfeather/azerothcore-wotlk
- This fork uses the default branch of mod-playerbots/azerothcore-wotlk as it's base. I need to do this to be able to make my own changes to AzerothCore. 
Going through the process of making pull requests for AzerothCore and test phases on the playerbots fork takes months. This no longer makes sense. I can't wait months for every small change I want to make.

## Please Note!
For item changes to take effect, please ensure *DBC.EnforceItemAttributes is set to 0*! This will allow the module to override item stats with their correct Vanilla/TBC values.

### Optional Data Files
Some changes for Vanilla and TBC content require DBC modification. The .mpq file for the client and .dbc files for the server can be found in the "optional" directory. These changes include requiring Vanilla reagents such as Flint and Steel for campfires, changing Level 60 enchanting to use Vanilla and not TBC reagents, profession leveling adjustments, etc. These files are all optional and the module will work without the patch, but these recipes will remain their WotLK versions.

### Data files through wowpack
This branch also ships the optional data files in the format of [wowpack](https://github.com/jl178/wowpack), so a realm that builds its client content pack with wowpack needs none of the manual steps in `optional/patch-explanations.txt`:

- `dbc/*.json` holds only the rows that differ from the stock 3.3.5a tables: Spell (3977 changed and 172 new rows with the default options), SkillLine, SkillLineAbility, SkillRaceClassInfo, SpellItemEnchantment, Item, ItemExtendedCost, Achievement, TaxiPath and TaxiPathNode. `wowpack build` merges them into complete DBC files for the client (in the Wowee pack and in `patch-Z.MPQ`) and writes the same rows as `spell_dbc`, `skilllineability_dbc`, ... SQL for the server, so no DBC file in the server's `data/dbc` needs replacing.
- Each choice of the manual install is a build option in `wowpack.json`; set one with `wowpack build --set mod-individual-progression.<option>=<value>`:

  | Option | Values | Default | What it ships |
  |---|---|---|---|
  | `mana_costs` | `vanilla`, `wotlk` | `vanilla` | `vanilla`: patch-V's Vanilla and TBC mana costs, which `IndividualProgression.EnableAllSpellRanks = 0` assumes (`options/mana_costs/vanilla/dbc/Spell.json`, 827 rows on top of `dbc/Spell.json`). `wotlk`: patch-S, WotLK mana costs; `dbc/Spell.json` alone is patch-S |
  | `classic_login_screen` | `true`, `false` | `true` | the Vanilla login screen, logo and music of patch-J (`options/classic_login_screen/true/client/`) |
  | `classic_loading_screens` | `true`, `false` | `true` | the Vanilla loading screens of patch-J, with patch-U's HD Eastern Kingdoms and Kalimdor (`options/classic_loading_screens/true/client/`) |

  `dbc/*.json` holds what patch-S and patch-V share. The loading screens are client-only; the mana costs change the server's `spell_dbc` rows too, so build the pack and the SQL with the same option.
- `id_ranges` reserves the ids of the rows the module adds that stock 3.3.5a does not have (172 Spell rows, 48 ItemExtendedCost, 49 TaxiPathNode, ...), so another module that picks one of them fails to build instead of merging into these rows.
- Spells 11202 and 25347 are also serverside placeholders in the core's `spell_dbc`. With the manual install the core's rows win and this module's versions of the two spells never reach the server; the SQL wowpack generates replaces them, and `wowpack build` warns about it.
- Only the enUS text is kept. The archives' DBCs also carry zhCN and zhTW strings; a pack built from enUS stock files would not use them.

The JSON files were made from the archives in `optional/` with

```
wowpack import -locales enUS -stock-dbc <stock 3.3.5a DBFilesClient> -module <scratch V> Patch-J.mpq Patch-U.mpq patch-V.mpq
wowpack import -locales enUS -stock-dbc <stock 3.3.5a DBFilesClient> -module <scratch S> patch-S.mpq
```

then splitting the two `Spell.json` files field by field: what both set went to `dbc/Spell.json`, what only V sets to `options/mana_costs/vanilla/dbc/Spell.json` (S sets nothing V does not), and the client files of patch-J and patch-U to the two client options. Run them again after the archives change. The server DBCs in `dbc.7z` and `patch-V.7z` are identical to the client DBCs in `patch-V.mpq`, so one import covers both.

### Optional Recommended Addons
- MultiBot: https://github.com/Wishmaster117/MultiBot-Chatless
- Character Services: https://github.com/Badgermilk0/mod-character-services
- Reagent Bank Account: https://github.com/Grimfeather/mod-reagent-bank-account

## Discord
[https://discord.gg/PTXxh3KSgQ](https://discord.gg/PTXxh3KSgQ)

## Thank You
Special thanks to the following contributors and projects that made this module possible:
* AzerothCore and TrinityCore, along with ancestors Mangos.
* AzerothCore mod-war-effort
* AzerothCore mod-pvp-titles
* vMangos for Vanilla game and patch progression data
* cMangos for TBC game data
* Sogladev for Naxx 40 scripts and data
* ZhengPeiRu21 for creating the original version of this module.
