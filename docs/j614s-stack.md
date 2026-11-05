# J614s NixOS package overlay

This branch is based on [Aurora 93582bd5b4af](https://github.com/aurora-silicon/nixos-aurora/commit/93582bd5b4af77d96ccef759778a06631811d232), refreshed on 5 November 2026. The previous fork history was preserved before rebuilding this overlay. The kernel (`2661d71e16bf`) and Aura (`9a173ff5bf62`, 1.0.0b5) now come directly from Aurora: their packaged sources include the J614s board, GPU, ISP, retained-input consumer and Aura handoff changes. No local kernel or Aura patch is needed.

Four component PRs are merged upstream but absent from this Aurora packaging revision. This fork adopts their merged upstream revisions instead of retaining duplicate component-fork commits:

| Component | Aurora package pin | Fork package pin | Included upstream work |
| --- | --- | --- | --- |
| u-boot | `d30213eadcaa886f4206d7661b61bc6cf7837828` | `279c4afa4bbaf600af1c2f493c75d8aadc206243` | [J614s retained MTP input handoff](https://github.com/aurora-silicon/u-boot/pull/29) |
| mesa | `05ddb00f5ca9d0e153d987fed6454f21902ee1c8` | `c1753704ed733e6e77fdf8ce6ac83fbf7c5e59d5` | [Wayland drawable lifetime and unmatched-release handling](https://github.com/aurora-silicon/mesa/pull/138) |
| alsa-ucm-conf-asahi | `a270b2e2d705ed521b07d008aac45283c75fb986` | `c3fe4b4abc5cc7b6a3326b24431257a6a4d8f692` | [J614 HPAI microphone UCM mapping](https://github.com/aurora-silicon/alsa-ucm-conf-asahi/pull/9) |
| asahi-audio | `d957fabbacbe7dd67b39f052b2ff21edea7ee038` | `f1d596b9efeede884b28e3ad81fff24b22ba97a3` | [J614 microphone DSP graph and WirePlumber rule](https://github.com/aurora-silicon/asahi-audio/pull/16) |

All source URLs name immutable commits in `aurora-silicon`; `sources.json` records verified archive SHA-256 digests and Nix SRI hashes.
Mesa host, FEX guest and Flatpak consumers use the same pin. Kernel, Aura and Mesa source tracking remains manual, using the upstream branches. U-Boot and the microphone sources are not covered by the upstream automatic tracker.

The Mesa merge discussion records the maintainer's Neo Wayland regression run; the audio/UCM merge discussion records installation checks and relies on the previously reported J614s recording. The UCM revision also includes the earlier DCP DisplayPort/HDMI audio definitions. These upstream results and previous combined-stack J614s boots do not qualify this refreshed package set. Internal speakers remain disabled by the upstream J614s board support.

No hardware packages were installed and no boot artifacts were changed by this refresh. Preserve the current working boot chain and recovery media before testing a new package set. Validation of the refreshed recipes and Nix derivations is separate from a hardware boot.

The installer image script remains disabled pending installation and update qualification. No fork installer image or signed binary cache is published. Upstream testing channels and `aurora-update` do not retain this fork overlay; pin `github:mchalunderscore/nixos-aurora?ref=j614s%2Ffull` explicitly in a consuming system flake and review its lock before rebuilding.

The separate t6040 system flake uses newer Nixpkgs than this repository's lock. A read-only evaluation with this refreshed input reaches a removed `boot.initrd.systemd.enable` option in the newly imported `modules/boot-splash.nix`. Its host-local Aurora compatibility adapter must be reviewed before changing that system's input lock; the fork's own locked configurations evaluate successfully. This refresh does not change the system flake or activate a new generation.

Refresh validation: all-platform flake evaluation passed, as did the portable source suite (223 tests, 2 skipped). Native U-Boot, microphone DSP, UCM and Mesa package builds passed, including all 122 Mesa tests. The checked Stage 2 bundle built with 116 board identifiers including `apple,j614s`. Built audio/UCM outputs contain the J614 microphone graph, WirePlumber match and HPAI profile. No system generation was activated and the bundle was not written to the ESP.
