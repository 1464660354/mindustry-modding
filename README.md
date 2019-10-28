html-postamble:nil

*Disclaimier: This is a work in progress, things may not work as expected.*

Overview
========

Directory Structure
-------------------

At the root of your project you should have a `content/` directory, this is where all the JSON data goes, and in that directory you have other directories for various types of units, these are the current common ones:

-   `content/items/` for things like copper and surge-alloy,
-   `content/blocks/` for things like turrets and floors,
-   `content/mechs/` for player controlled mechs tau and dagger,
-   `content/liquids/` for liquids like water and slag,
-   `content/units/` for flying or ground units like reaper and dagger,
-   `content/zones/` for configuration of campaign maps.

Feilds have noumerous attributes, but the important one is `type` as it's what tells the parser which class of object to select. A lot of objects are actually very simple, and follow the common OOP patterns, all that means is they inherit attributes and various behaviors. **(sometimes predictably, othertimes not as much)**

Dependencies
------------

You can add dependencies to your mod, and be able to have acces to them. Add the root directory name of the other mod, this in your `mod.json`:

``` json
{
    "dependencies": [
        "other-mod-name",
        "not-a-mod"
    ]
}
```

The names is the mods name lower cased, with special characters replaced with `-`, and then from your mod, you can now use anything in the other mod by prefixing it with said name, for example:

-   `other-mod-name-not-copper` would referance `not-copper` in `other-mod-name`,
-   `other-mod-name-angry-dagger` would referance `angry-dagger` in `other-mod-name`,
-   `not-a-mod-angry-dagger` would referance `angry-dagger` in `not-a-mod`.

Inheritance
-----------

You may be confused as to what \`extends\` mean. In the context of modding, all it means is some fields are inherited. A \`Turret\` type, is also a \`Block\` type, so it can also have \`health\` which happens to be in \`Block\` because it \`extends Block\`.

Whether the behavior is what you'd expect is at the objects discresion, some fields aren't actually really used by their extenders, resulting in no behavior at all.

ItemStack
---------

This type is used by blocks, to consume/output items.

An item stack is simply an array of objects with these fields:

| field  | type | default |
|--------|------|---------|
| amount | int  | 1       |
| item   | Item |         |

For example:

``` json
[
    { "amount": 30, "item": "surge-alloy" },
    { "amount": 90, "item": "copper" }
]
```

LiquidStack
-----------

This type is used by blocks, to consume a liquid, just like \`ItemStack\` except that it can only contain 1 liquid.

| field  | type   |
|--------|--------|
| liquid | Liquid |
| amount | float  |

For example:

``` json
{
   "liquid": "water",
   "amount": 0.5
}
```

UnlockableContent and MappableContent
-------------------------------------

This is for types that can have a display name and description.

| field       | type   |
|-------------|--------|
| name        | String |
| description | String |

### Item

`extends UnlockableContent` Objects that ride conveyors/sorters and can be used in crafters.

| field          | type     | default | notes                                                                 |
|----------------|----------|---------|-----------------------------------------------------------------------|
| color          | Color    |         | hex string of color                                                   |
| type           | ItemType |         | resource or material; used for tabs and core acceptance               |
| explosiveness  | float    | 0       | how explosive this item is.                                           |
| flammability   | float    | 0       | flammability above 0.3 makes this eleigible for item burners.         |
| radioactivity  | float    |         | how radioactive this item is. 0=none, 1=chernobyl ground zero         |
| hardness       | int      | 0       | drill hardness of the item                                            |
| cost           | float    | 1       | used for calculating place times; 1 cost = 1 tick added to build time |
| alwaysUnlocked | boolean  | false   | If true, item is always unlocked.                                     |

### Liquid

`extends UnlockableContent` -- Object that defines the properties of a liquid.

| field         | type         | default | notes                                                                                |
|---------------|--------------|---------|--------------------------------------------------------------------------------------|
| color         | Color        |         | color of liquid                                                                      |
| flammability  | float        |         | 0 to 1; 0 is completely inflammable, above that may catch fire when exposed to heat. |
| temperature   | float        | 0.5     | 0.5 is 'room' temperature, 0 is very cold, 1 is molten hot                           |
| heatCapacity  | float        | 0.5     | used in cooling; water is 0.4                                                        |
| viscosity     | float        | 0.5     | how thick this liquid is; water is 0.5, tar is 1 (very slow)                         |
| explosiveness | float        |         | explosiveness when heated; 0 is nothing, 1 is nuke                                   |
| flameColor    | Color        | ffb763  | the burning color of this liquid                                                     |
| effect        | StatusEffect | none    | the associated status effect.                                                        |

### BlockStorage

`extends UnlockableContent`

| field         | type       | default | notes |
|---------------|------------|---------|-------|
| hasItems      | boolean    |         |       |
| hasLiquids    | boolean    |         |       |
| hasPower      | boolean    |         |       |
| outputsLiquid | boolean    | false   |       |
| consumesPower | boolean    | true    |       |
| outputsPower  | bolean     | false   |       |
| itemCapacity  | int        | 10      |       |
| item          | float      | 10      |       |
| stats         | BlockStats |         |       |
| bars          | BlockBars  |         |       |
| consumes      | Consumers  |         |       |

### BuildVisibility

Options for build visibility include: \* `hidden` \* `shown` \* `debugOnly` \* `sandboxOnly` \* `campaignOnly`

### BlockGroup

Groups for blocks to build on top of each other: \* `none` \* `walls` \* `turrets` \* `transportation` \* `power` \* `liquids` \* `drills`

### Category

Categories for building menu: \* `turret` Offensive turrets; \* `production` Blocks that produce raw resources, such as drills; \* `distribution` Blocks that move items around; \* `liquid` Blocks that move liquids around; \* `power` Blocks that generate or transport power; \* `defense` Walls and other defensive structures; \* `crafting` Blocks that craft things; \* `units` Blocks that create units; \* `upgrade` Things that upgrade the player such as mech pads; \* `effect` Things for storage or passive effects.

### Block

`extends BlockStorage` -- Attributes for all objects that are blocks.

| field               | type            | default      | notes                                                                              |
|---------------------|-----------------|--------------|------------------------------------------------------------------------------------|
| update              | boolean         |              | whether this block has a tile entity that updates                                  |
| destructible        | boolean         |              | whether this block has health and can be destroyed                                 |
| unloadable          | boolean         | true         | whether unloaders work on this block                                               |
| solid               | boolean         |              | whether this is solid                                                              |
| solidifes           | boolean         |              | whether this block CAN be solid.                                                   |
| rotate              | boolean         |              | whether this is rotateable                                                         |
| breakable           | boolean         |              | whether you can break this with rightclick                                         |
| placeableOn         | boolean         | true         | whether this floor can be placed on.                                               |
| health              | int             | -1           | tile entity health                                                                 |
| baseExplosiveness   | float           | 0            | base block explosiveness                                                           |
| floating            | boolean         | false        | whether this block can be placed on edges of liquids.                              |
| size                | int             | 1            | multiblock size                                                                    |
| expanded            | boolean         | false        | Whether to draw this block in the expanded draw range.                             |
| timers              | int             | 0            | Max of timers used.                                                                |
| fillesTile          | true            |              | Special flag; if false, floor will be drawn under this block even if it is cached. |
| alwaysReplace       | boolean         | false        | whether this block can be replaced in all cases                                    |
| group               | BlockGroup      | none         | Unless `canReplace` is overriden, blocks in the same group can replace each other. |
| priority            | TargetPriority  | base         | Targeting priority of this block, as seen by enemies.                              |
| configurable        | boolean         |              | Whether the block can be tapped and selected to configure.                         |
| consumesTap         | boolean         |              | Whether this block consumes touchDown events when tapped.                          |
| posConfig           | boolean         |              | Whether the config is positional and needs to be shifted.                          |
| targetable          | boolean         | true         | Whether units target this block.                                                   |
| canOverdrive        | boolean         | true         | Whether the overdrive core has any effect on this block.                           |
| outlineColor        | Color           | 404049       | Outlined icon color.                                                               |
| outlineIcon         | boolean         | false        | Whether the icon region has an outline added.                                      |
| hasShadow           | boolean         | true         | Whether this block has a shadow under it.                                          |
| breakSound          | Sound           | boom         | Sounds made when this block breaks.                                                |
| activeSound         | Sound           | none         | The sound that this block makes while active. One sound loop. Do not overuse.      |
| activeSoundVolume   | float           | 0.5          | Active sound base volume.                                                          |
| idleSound           | Sound           | none         | The sound that this block makes while idle. Uses one sound loop for all blocks.    |
| idleSoundVolume     | float           | 0.5          | Idle sound base volume.                                                            |
| requirements        | ItemStack       | \[\]         | Cost of constructing this block.                                                   |
| category            | Category        | distribution | Category in place menu.                                                            |
| buildCost           | float           |              | Cost of building this block; do not modify directly!                               |
| buildVisibility     | BuildVisibility | hidden       | Whether this block is visible and can currently be built.                          |
| buildCostMultiplier | float           | 1            | Multiplier for speed of building this block.                                       |
| instantTransfer     | boolean         | false        | Whether this block has instant transfer.                                           |
| alwaysUnlocked      | boolean         | false        |                                                                                    |

1.  Wall

    `extends Block`

    | field    | type | default |
    |----------|------|---------|
    | variants | int  | 0       |

    Defaults

    | field               | default |
    |---------------------|---------|
    | solid               | true    |
    | destructible        | true    |
    | group               | walls   |
    | buildCostMultiplier | 5       |

    1.  Door

        `extends Wall`

        | field   | type   | default   |
        |---------|--------|-----------|
        | openfx  | Effect | dooropen  |
        | closefx | Effect | doorclose |

        Defaults:

        | field       | type  |
        |-------------|-------|
        | solid       | false |
        | solidfies   | true  |
        | consumesTap | true  |

### Effect

Value type should be `string`. This type will animate a pre-programmed effects. List of built-in effects:

-   none, placeBlock, breakBlock, smoke, spawn, tapBlock, select;
-   vtolHover, unitDrop, unitPickup, unitLand, pickup, healWave, heal, landShock, reactorsmoke, nuclearsmoke, nuclearcloud;
-   redgeneratespark, generatespark, fuelburn, plasticburn, pulverize, pulverizeRed, pulverizeRedder, pulverizeSmall, pulverizeMedium;
-   producesmoke, smeltsmoke, formsmoke, blastsmoke, lava, doorclose, dooropen, dooropenlarge, doorcloselarge, purify, purifyoil, purifystone, generate;
-   mine, mineBig, mineHuge, smelt, teleportActivate, teleport, teleportOut, ripple, bubble, launch;
-   healBlock, healBlockFull, healWaveMend, overdriveWave, overdriveBlockFull, shieldBreak, hitBulletSmall, hitFuse;
-   hitBulletBig, hitFlameSmall, hitLiquid, hitLaser, hitLancer, hitMeltdown, despawn, flakExplosion, blastExplosion;
-   plasticExplosion, artilleryTrail, incendTrail, missileTrail, absorb, flakExplosionBig, plasticExplosionFlak, burning, fire;
-   fireSmoke, steam, fireballsmoke, ballfire, freezing, melting, wet, oily, overdriven, dropItem, shockwave;
-   bigShockwave, nuclearShockwave, explosion, blockExplosion, blockExplosionSmoke, shootSmall, shootHeal, shootSmallSmoke, shootBig, shootBig2, shootBigSmoke;
-   shootBigSmoke2, shootSmallFlame, shootPyraFlame, shootLiquid, shellEjectSmall, shellEjectMedium;
-   shellEjectBig, lancerLaserShoot, lancerLaserShootSmoke, lancerLaserCharge, lancerLaserChargeBegin, lightningCharge, lightningShoot;
-   unitSpawn, spawnShockwave, magmasmoke, impactShockwave, impactcloud, impactsmoke, dynamicExplosion, padlaunch, commandSend, coreLand.

You can't currently create custom effects.

### BulletType

| field              | type         |       | notes                                                                   |
|--------------------|--------------|-------|-------------------------------------------------------------------------|
| lifetime           | float        |       | amount of ticks it lasts                                                |
| speed              | float        |       | inital speed of bullet                                                  |
| damage             | float        |       | collision damage                                                        |
| hitSize            | float        | 4     | collision radius                                                        |
| drawSize           | float        | 40    |                                                                         |
| drag               | float        | 0     | decelleration per tick                                                  |
| pierce             | boolean      |       | whether it can collide                                                  |
| hitEffect          | Effect       |       | created when bullet hits something                                      |
| despawnEffect      | Effect       |       | created when bullet despawns                                            |
| shootEffect        | Effect       |       | created when shooting                                                   |
| smokeEffect        | Effect       |       | created when shooting                                                   |
| hitSound           | Sound        |       | made when hitting something or getting removed                          |
| inaccuracy         | float        | 0     | extra inaccuracy                                                        |
| ammoMultiplier     | float        | 2     | how many bullets get created per item/liquid                            |
| reloadMultiplier   | float        | 1     | multiplied by turret reload speed                                       |
| recoil             | float        |       | recoil from shooter entities                                            |
| splashDamage       | float        | 0     |                                                                         |
| knockback          | float        |       | Knockback in velocity.                                                  |
| hitTiles           | boolean      | true  | Whether this bullet hits tiles.                                         |
| status             | StatusEffect | none  | Status effect applied on hit.                                           |
| statusDuration     | float        | 600   | Intensity of applied status effect in terms of duration.                |
| collidesTiles      | boolean      | true  | Whether this bullet type collides with tiles.                           |
| collidesTeam       | boolean      | false | Whether this bullet type collides with tiles that are of the same team. |
| collidesAir        | boolean      | true  | Whether this bullet type collides with air units.                       |
| collides           | boolean      | true  | Whether this bullet types collides with anything at all.                |
| keepVelocity       | boolean      | true  | Whether velocity is inherited from the shooter.                         |
| fragBullets        | int          | 9     |                                                                         |
| fragVelocityMin    | float        | 0.2   |                                                                         |
| fragVelocityMax    | float        | 1     |                                                                         |
| fragBullet         | BulletType   | null  |                                                                         |
| splashDamageRadius | float        | -1    | Use a negative value to disable splash damage.                          |
| incendAmount       | int          | 0     |                                                                         |
| incendSpread       | float        | 8     |                                                                         |
| incendChance       | float        | 1     |                                                                         |
| homingPower        | float        | 0     |                                                                         |
| homingRange        | float        | 50    |                                                                         |
| lightining         | int          |       |                                                                         |
| lightningLength    | int          | 5     |                                                                         |
| hitShake           | float        | 0     |                                                                         |

1.  BasicBulletType

    The actual bullet type.

    | field        | type   | default          |
    |--------------|--------|------------------|
    | backColor    | Color  | bulletYellowBack |
    | frontColor   | Color  | bulletYellow     |
    | bulletWidth  | float  | 5                |
    | bulletHeight | float  | 7                |
    | bulletShrink | float  | 0.5              |
    | bulletSprite | String |                  |

    1.  ArtilleryBulletType

        | field       | type   | default        |
        |-------------|--------|----------------|
        | trailEffect | Effect | artilleryTrail |

        Defaults:

        | field         | type      |
        |---------------|-----------|
        | collidesTiles | false     |
        | collides      | false     |
        | hitShake      | 1         |
        | hitSound      | explosion |

    2.  BombBulletType

        Defaults:

        | field         | type      |
        |---------------|-----------|
        | collidesTiles | false     |
        | collides      | false     |
        | bulletShrink  | 0.7       |
        | lifetime      | 30        |
        | drag          | 0.05      |
        | keepVelocity  | false     |
        | collidesAir   | false     |
        | hitSound      | explosion |

    3.  FlakBulletType

        Bullets that explode near enemies.

        | field        | type  | default | notes |
        |--------------|-------|---------|-------|
        | explodeRange | float | 30      |       |

        Defaults:

        | field              | type             |
        |--------------------|------------------|
        | splashDamage       | 15               |
        | splashDamageRadius | 34               |
        | hitEffect          | flakExplosionBig |
        | bulletWidth        | 8                |
        | bulletHeight       | 10               |

    4.  HealBulletType

        Bullets that can heal blocks of the same team as the shooter.

        | field       | type  | default | notes |
        |-------------|-------|---------|-------|
        | healPercent | float | 3       |       |

        Defaults:

        | field         | type      |
        |---------------|-----------|
        | shootEffect   | shootHeal |
        | smokeEffect   | hitLaser  |
        | hitEffect     | hitLaser  |
        | despawnEffect | hitLaser  |
        | collidesTeam  | true      |

    5.  LiquidBulletType

        | field  | type   | default |                |
        |--------|--------|---------|----------------|
        | liquid | Liquid | null    | required field |

        Defaults:

        | field          | type      |
        |----------------|-----------|
        | lifetime       | 74        |
        | statusDuration | 90        |
        | despawnEffect  | none      |
        | hitEffect      | hitLiquid |
        | smokeEffect    | none      |
        | shootEffect    | none      |
        | drag           | 0.009     |
        | knockback      | 0.55      |

    6.  MassDriverBolt

        Defaults:

        | field         | type         |
        |---------------|--------------|
        | collidesTiles | false        |
        | lifetime      | 200          |
        | despawnEffect | smeltsmoke   |
        | hitEffect     | hitBulletBig |
        | drag          | 0.005        |

    7.  MissileBulletType

        | field      | type  | default           |
        |------------|-------|-------------------|
        | trailColor | Color | missileYellowBack |
        | weaveScale | float | 0                 |
        | weaveMag   | float | -1                |

### Mech

`extends Unlockablecontent` -- Mechs are the player controlled entities.

| field              | type    | default |
|--------------------|---------|---------|
| flying             | boolean |         |
| speed              | float   | 1.1     |
| maxSpeed           | float   | 10      |
| boostSpeed         | float   | 0.75    |
| drag               | float   | 0.4     |
| mass               | float   | 1       |
| shake              | float   | 0       |
| health             | float   | 200     |
| hitsize            | float   | 6       |
| cellTrnsY          | float   | 0       |
| mineSpeed          | float   | 1       |
| drillPower         | int     | -1      |
| buildPower         | float   | 1       |
| engineColor        | Color   | boostTo |
| itemCapacity       | int     | 30      |
| turnCursor         | boolean | true    |
| canHeal            | boolean | false   |
| compoundSpeed      | float   | 5       |
| compoundSpeedBoost | float   | 5       |
| weaponOffsetY      | float   | 5       |
| engineOffset       | float   | 5       |
| engineSize         | float   | 2.5     |
| weapon             | Weapon  | null    |

### Weapon

Weapons are used by units types, flying or ground, and mechs alike. They're what actually shoots the bullets.

| field          | type        | default | notes                                                             |
|----------------|-------------|---------|-------------------------------------------------------------------|
| name           | string      |         | weapons name                                                      |
| nimPlayerDist  | float       | 20      | minimum cursor distance from player, fixes 'cross-eyed' shooting. |
| sequenceNum    | int         | 0       |                                                                   |
| bullet         | BulletType  |         | bullet shot                                                       |
| ejectEffect    | ejectEffect | none    | shell ejection effect                                             |
| reload         | float       |         | weapon reload in frames                                           |
| shots          | int         | 1       | amount of shots per fire                                          |
| spacing        | float       | 12      | spacing in degrees between multiple shots, if applicable          |
| inaccuracy     | float       | 0       | inaccuracy of degrees of each shot                                |
| shake          | float       | 0       | intensity and duration of each shot's screen shake                |
| recoil         | float       | 1.5     | visual weapon knockback.                                          |
| length         | float       | 3       | shoot barrel y offset                                             |
| width          | float       | 4       | shoot barrel x offset.                                            |
| velocityRnd    | float       | 0       | fraction of velocity that is random                               |
| alternate      | bool        | false   | shoot one arm after another, rather than all at once              |
| lengthRand     | float       | 0       | randomization of shot length                                      |
| shotDelay      | float       | 0       | delay in ticks between shots                                      |
| ignoreRotation | boolean     | false   | whether shooter rotation is ignored when shooting.                |
| shootSound     | Sound       | pew     |                                                                   |
