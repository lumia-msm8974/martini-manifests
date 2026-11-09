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

Credits: LineageOS (msm8974 common, bacon and kernel trees), TheMuppets
(vendor blobs), Markynose/lumia930-linux (Lumia 930 hardware map). See the
device tree README for the full list.
