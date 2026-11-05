# PixelExperience-Reborn Android 17 — legacy Qualcomm audio audit (mondrian)

**2026-11-05 | P2 `hardware/qcom/audio` | historical source; no Android17 product consumer established.**

## Verified source contract

- Historical PE head `734b1d916c91249ee0b7a54cd41195d35a6de3da`; includes `hal`, `legacy`, `post_proc`, `visualizer`, `voice_processing`, with `Android.mk` entry point.
- The top-level `Android.mk` activates for a fixed historical platform list: `msm8960 msm8226 msm8x26 msm8084 msm8992 msm8994 msm8996 msm8909 msm8952 msm8998 sdm845 sdm710`. It **does not include `taro`**.
- The actual mondrian `LF52406/mondrian_android_device_xiaomi_sm8450-common` `lineage-24.0` `BoardConfigCommon.mk` declares `TARGET_BOARD_PLATFORM := taro` and `TARGET_USES_QCOM_MM_AUDIO := true`. It selects PAL-HIDL-related audio flags.
- The real device `common.mk` requires `audio.primary.taro`, `android.hardware.audio@7.0-impl`, `android.hardware.audio.service`, `vendor.qti.hardware.AGMIPC@1.0-service`, `libagm_*_plugin`, `libfmpal`, `libqcompostprocbundle`, `libqcomvisualizer` and `libqcomvoiceprocessing`.
- The actual primary HAL and Codec2 config resources are referenced under `hardware/qcom-caf/sm8450/audio/primary-hal/configs/` (taro and common paths). These do not originate from the legacy `hardware/qcom/audio/hal` build.
- The historical `hal/Android.mk` produces `audio.primary.$(TARGET_BOARD_PLATFORM)` using `msm8974`-derived code for selected old SoCs and old `BOARD_USES_ALSA_AUDIO` gates, not a modern Taro AGM/PAL implementation.
- The latest PE change `734b1d9` is a real legacy CFI function signature fix (CVE-2021-30353 context); it must not be assumed to transfer to current Taro PAL sources without a matching function and consumer.

## Decision and safety gates

**Retain historical PE audio source as a reference only. Do not enable, merge wholesale or add this repo to the official AOSP17 staged source graph.** A blind enabling risks unsupported module variants and conflicting `audio.primary` / effects ownership, especially if a board flag changes.

1. Continue the *next actual* mondrian audio work at the preceding P2 rows `hardware/qcom-caf/sm8450/audio/agm`, `pal`, `primary-hal`, already tracked as an interdependent set.
2. Select one matching Taro Audio HAL, effects and Codec2 module owner; validate `libtinycompress` ABI, AGMIPC/PAL interface, VINTF audio service, Soong namespaces, product installation and vendor SDK compatibility.
3. If current Taro code independently contains the old vulnerability pattern, port a narrowly reviewed fix there. Do not backport obsolete `msm8974` sources just because historical PE included it.
4. Validate sound output/input, voice calls, Bluetooth, USB audio, hotplug, recording, effects, offload and amplifier behavior on physical mondrian after Android17 Soong and SELinux enforcing compilation.

**Source decision only.** No Android17 audio HAL/effects/Codec2 build or device runtime test was run. The default `seventeen` history is unchanged.

**Next matrix row:** `hardware/qcom/camera`.
