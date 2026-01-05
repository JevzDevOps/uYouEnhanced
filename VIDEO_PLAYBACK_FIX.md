# Video Playback Fix

## Issue
Videos were showing "something went wrong tap to retry" errors when watching both live streams and VODs, including when the screen was locked (background playback).

## Root Cause
The ad-blocking workaround patches in `Sources/uYouPlus.xm` were calling `%orig(nil)` instead of just returning, which passed `nil` to YouTube's player response context decorator methods. This broke the player response structure that YouTube's player needs to function properly.

**Problematic code:**
```objc
%hook YTAdsInnerTubeContextDecorator
- (void)decorateContext:(id)context { %orig(nil); }  // ❌ Passing nil breaks player
%end
```

## Fix Applied
Changed the ad-blocking patches to simply return without calling the original method with `nil`. This prevents breaking the player response while still blocking ads.

**Fixed code:**
```objc
%hook YTAdsInnerTubeContextDecorator
- (void)decorateContext:(id)context { 
    // Don't call original with nil - just return to prevent breaking player response
    return;
}
%end
```

## Files Modified
- `Sources/uYouPlus.xm` - Fixed both `uYouAdBlockingWorkaroundLite` and `uYouAdBlockingWorkaround` groups

## Testing
After rebuilding with this fix:
1. Videos should play normally without "something went wrong" errors
2. Live streams should work properly
3. Background playback (screen locked) should work correctly
4. Ad blocking should still function (ads removed from videos/shorts)

## Next Steps
1. Rebuild the IPA using GitHub Actions or locally
2. Test video playback with both VODs and live streams
3. Test background playback by locking the screen while a video is playing
4. Verify that ads are still being blocked

## Note
If you still experience issues after this fix, you may want to disable the ad-blocking workarounds in the app settings:
- Go to YouTube Settings → uYouEnhanced Settings
- Disable "AdBlock Workaround Lite" or "AdBlock Workaround" if enabled

