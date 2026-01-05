# Video Playback Fix

## Issue
Videos were showing "something went wrong tap to retry" errors when watching both live streams and VODs, including when the screen was locked (background playback).

## Root Causes Identified

### 1. Ad-Blocking Context Decorator Issue
The ad-blocking workaround patches were calling `%orig(nil)` which passed `nil` to YouTube's player response context decorator methods, breaking the player response structure.

### 2. Ad-Blocking Enabled by Default
The ad-blocking workarounds (`kAdBlockWorkaroundLite` and `kAdBlockWorkaround`) were enabled by default, which can cause playback issues with certain YouTube versions.

## Fixes Applied

### Fix 1: Context Decorator Methods
Changed the ad-blocking patches to call the original method with the actual context instead of `nil`:

**Before:**
```objc
%hook YTAdsInnerTubeContextDecorator
- (void)decorateContext:(id)context { %orig(nil); }  // ❌ Passing nil breaks player
%end
```

**After:**
```objc
%hook YTAdsInnerTubeContextDecorator
- (void)decorateContext:(id)context { 
    // Call original with actual context to prevent breaking player response
    if (context) {
        %orig(context);
    }
}
%end
```

### Fix 2: Disable Ad-Blocking by Default
Changed the default settings to disable ad-blocking workarounds by default to prevent playback issues:

**Before:**
```objc
[[NSUserDefaults standardUserDefaults] setBool:YES forKey:kAdBlockWorkaroundLite];
```

**After:**
```objc
// Disabled by default to prevent playback issues
[[NSUserDefaults standardUserDefaults] setBool:NO forKey:kAdBlockWorkaroundLite];
```

## Files Modified
- `Sources/uYouPlus.xm` - Fixed both `uYouAdBlockingWorkaroundLite` and `uYouAdBlockingWorkaround` groups
  - Fixed context decorator methods to call original with actual context
  - Changed default settings to disable ad-blocking workarounds

## Testing
After rebuilding with these fixes:
1. Videos should play normally without "something went wrong" errors
2. Live streams should work properly
3. Background playback (screen locked) should work correctly
4. Ad blocking can be enabled manually in settings if needed (but may cause playback issues)

## Next Steps
1. **Rebuild the IPA** using GitHub Actions or locally
2. **Test video playback** with both VODs and live streams
3. **Test background playback** by locking the screen while a video is playing
4. **If you need ad blocking**, enable it manually in settings:
   - Go to YouTube Settings → uYouEnhanced Settings
   - Enable "AdBlock Workaround Lite" or "AdBlock Workaround" if you want ad blocking
   - **Note:** Enabling ad blocking may cause playback issues with some YouTube versions

## Important Notes
- **Ad-blocking is now disabled by default** to prevent playback issues
- If you experience playback errors, make sure ad-blocking workarounds are disabled
- The ad-blocking patches may be incompatible with certain YouTube versions
- uYou's built-in ad blocking (if available) may work better than these workarounds

