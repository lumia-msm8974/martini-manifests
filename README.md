# Manifests for the Nokia Lumia 930 (martini)

**Status: not booted — the device is not available yet.** The trees these
manifests fetch have never run on a Lumia 930.

- `lineageos-18.1/martini_manifest.xml`: local manifest for LineageOS 18.1.
  Same projects as the Lumia 1520 one
  ([bandit-manifests](https://github.com/lumia-msm8974/bandit-manifests)),
  with the martini device and vendor trees and the `lineage-18.1-martini`
  branch of the kernel shared by the MSM8974 Lumias.

```
repo init -u https://github.com/LineageOS/android.git -b lineage-18.1 --git-lfs --depth=1
mkdir -p .repo/local_manifests
curl -fsSL https://raw.githubusercontent.com/lumia-msm8974/martini-manifests/lineage-18.1/lineageos-18.1/martini_manifest.xml \
  -o .repo/local_manifests/martini.xml
repo sync -c -j8 --no-tags --no-clone-bundle
source build/envsetup.sh && lunch lineage_martini-userdebug
```

The kernel project has the same path (`kernel/nokia/msm8974`) as in the
1520 manifest: use one checkout per device, or switch the branch, rather
than both local manifests in the same tree.

## Framework patches

`lineageos-18.1/patches/` holds the changes to LineageOS projects that have no fork: apply them after
`repo sync`, from a clone of this repository.

```
git clone https://github.com/lumia-msm8974/martini-manifests
(cd frameworks/base && git am ../../martini-manifests/lineageos-18.1/patches/frameworks/base/*.patch)
(cd packages/apps/Camera2 && git am ../../../martini-manifests/lineageos-18.1/patches/packages/apps/Camera2/*.patch)
(cd frameworks/opt/net/wifi && git am ../../../../martini-manifests/lineageos-18.1/patches/frameworks/opt/net/wifi/*.patch)
(cd packages/apps/Updater && git am ../../../martini-manifests/lineageos-18.1/patches/packages/apps/Updater/*.patch)
```

- `frameworks/base/0001`: SystemUI builds one `ThumbnailData` per task snapshot (recents).
- `frameworks/base/0002`: `TextView` reads `ro.config.marquee_fade_scrolling`; the msm8974 common tree sets it
  to `false`, so a scrolling marquee has no fading edges (each one costs the Adreno 330 a framebuffer copy in
  the middle of the frame).
- `frameworks/base/0003`: ActivityManager reads its cached process limit from
  `ro.config.max_cached_processes` (32 when unset); the msm8974 common tree sets 64, so the low memory killer,
  not a count, decides how many background apps stay alive.
- `frameworks/base/0004`: SettingsProvider loads a default for the animator duration scale
  (`def_animator_duration_scale`); the msm8974 common tree overlays it, with the window and transition scales, to
  0.5x for new installations.
- `frameworks/opt/net/wifi/0001`: the network selection gives the connected BSSID 40 more points (10 dB). The
  current network bonus goes to every BSSID of the network, so on a dual band router with a weak 5 GHz signal the
  phone moved between 2.4 and 5 GHz every 20 s while idle (12-15 reassociations in 5 minutes, then 1).
- `packages/apps/Updater/0001`: downloads get a 15 s connect and a 30 s read timeout, and a download that fails
  resumes by itself after 10 s, only on Wi-Fi or Ethernet (up to 5 resumes in a row that get no data, and up to 10
  minutes of waiting for Wi-Fi). Without it a download stalled for good when the router moved the phone to the other
  band, until the user paused and resumed it.
- `packages/apps/Camera2/0001`: back camera video mode in the settings, shown only on martini: Standard, 1080p at
  60 fps, 4K at 30 fps (centre crop).

Credits: LineageOS (msm8974 common, bacon and kernel trees), TheMuppets
(vendor blobs), Markynose/lumia930-linux (Lumia 930 hardware map). See the
device tree README for the full list.
