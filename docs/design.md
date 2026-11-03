# QuanShipyard: design (vision, draft)

Status: vision only, 2 Nov 2026. Nothing designed in detail or built. To be scaffolded later.

## Vision

Upgrade and customise ships, starting from what **[LongshipUpgrades](https://thunderstore.io/c/valheim/p/shudnal/LongshipUpgrades/)**
(shudnal, 1.0.24, source on [GitHub](https://github.com/shudnal/LongshipUpgrades)) does for the longship, then:
- **extending it to the drakkar**, and
- **adding a few upgrades of our own** along the way.

## Inspiration and credit

QuanShipyard is inspired by shudnal's LongshipUpgrades. Its README will credit shudnal prominently for the idea and
the design of the upgrades it emulates.

**Licence:** LongshipUpgrades is released under **The Unlicense** (checked 2 Nov 2026), a public-domain dedication. We
may fork it or reuse its code freely, with no attribution required; we credit shudnal anyway.

## What LongshipUpgrades does (the starting point)

From its Thunderstore page, 1.0.24:
- **Mast** upgrade: the mast becomes removable, and carries the lantern, tent and sail colours.
- **Lantern** hanging from the mast beam to light the deck.
- **Tent** (awning): shelter and resting while sailing; removable if it's in the way.
- **Shields** along the hull: more ship health, less arrow damage.
- **Fire-resistant hull:** resists fire and protects against the Ashlands ocean.
- **Ballista turrets:** a pair on the bow.
- **Trophy figurehead:** any trophy on the bow; boss trophies grant their forsaken power (personal, shared cooldown).
- **Storage:** two steps, each +1 width and height; the extra space clutters the deck.
- **Styles:** switchable heads, sails, tents and shields, with custom textures.
- **Wisp torch** on the mast to push back mist.
- Every upgrade has configurable materials, crafting station and level; the station only needs to be within 100 m.
- Required on server and every client. Depends on shudnal's ConditionalConfigSync (installed separately).

## Our additions (ideas so far)

- **Drakkar support:** the same kinds of upgrades, fitted to the drakkar's model.
- **Shields on the side** and **lanterns** (both exist for the longship; carry them to the drakkar).
- More upgrades: to collect.

### Boss trophy figurehead: changes from LongshipUpgrades

Keep the mounted boss trophy granting its forsaken power, with these changes (Quandru, 2 Nov 2026):

1. **Its own cooldown, not the player's.** Using the trophy must not put your own forsaken power on cooldown, and
   your own power's cooldown must not block the trophy. The cooldown belongs to the boat's trophy (kept on the
   ship so every player sees the same timer, and it survives relogs).
2. **The whole crew gets it, like the real power.** Activating the trophy gives the power to every player within
   range, not just whoever activated it. That way the crew keeps the buff when the helmsman swaps. Crew who already
   have it are **refreshed to the full duration** (as vanilla does), not skipped.
3. **Still respects the active power.** You can't activate it while you already have that power's status effect on
   you.
4. **Fully separate from your own power.** Using the trophy never sets it as your selected forsaken power, even if
   you have none (LongshipUpgrades does this; we drop it).
5. **Range covers the whole ship.** About **20 m** for the longship, **25-30 m** for the drakkar: set per ship type
   and configurable; confirm the drakkar figure against its hull length in game.

How LongshipUpgrades does it now (`ShipTrophyStand.cs`, checked 2 Nov 2026):
- `DelayedPowerActivation` adds the status effect to `Player.m_localPlayer` only, and sets that player's
  `m_guardianPowerCooldown` to the power's cooldown. That's the shared cooldown and the "only me" behaviour.
- If the player has no forsaken power selected, it also sets the trophy's power as their selected one.
- `Interact` refuses if the player already has the power's status effect (`IsGuardianPowerActive`) or their own
  power is on cooldown (`IsGuardianPowerOnCooldown`). Keep the first check; drop the second for change 1.

How vanilla does it (`Player.ActivateGuardianPower`): `GetPlayersInRange(transform.position, 10f, ...)`, then
`AddStatusEffect(m_guardianSE.NameHash(), resetTime: true)` on each player found, then sets only the activator's
cooldown. Change 2 copies this (including `resetTime: true`, which gives the refresh), but with the ship-sized range
from change 5 instead of 10 m, and without touching anyone's own cooldown.

## Open questions (for later)

1. **Fork or rewrite?** The licence allows either. Forking gives us everything that works today, and keeps the save
   data of the server's existing upgraded longships readable. **The live server runs LongshipUpgrades now**, so
   whichever we choose, longships upgraded with it must keep their upgrades when we switch. A fork would need moving
   to our template's conventions: Jotunn and its config sync instead of ConditionalConfigSync, numbered config
   sections, and a dependency doc.
2. **Which upgrades on the drakkar**, and what new ones?
3. **Other ships** (karve, raft, ship mods), or longship and drakkar only?
