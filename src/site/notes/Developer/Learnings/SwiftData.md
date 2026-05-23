---
{"dg-publish":true,"permalink":"/developer/learnings/swift-data/","dg-note-properties":{}}
---

Apple markets SwiftData as the easy, modern replacement for Core Data. And honestly, when you first use it, it feels that way. The API is clean, the `@Model` macro is elegant, and you can get a working persistence layer in a few lines of code. It integrates beautifully with SwiftUI, relationships are intuitive, and it feels like the kind of framework Apple should have shipped years ago.

Then you ship your app.

I have an app in the app store https://striveplanner.app that's been using SwiftData for almost three years. What follows is everything I've learned the hard way. Literally the hard way. This is why I'd recommend against using SwiftData for any app you plan to maintain long-term.

## Migrations are a dead end

SwiftData only supports simple, automatic "lightweight" migrations - things like adding an optional field. That's fine for version 1.1. But real apps evolve. You need to restructure data, rename fields, clean up bad decisions, consolidate models. SwiftData has no mechanism for any of this.

If you make a bad schema decision early on, you're stuck with it. Forever. There's no migration path that lets you reshape your data. You end up keeping legacy workarounds in your code for the entire life of your app, and those workarounds propagate throughout your entire codebase.

Your schema will always change. You will always need to do migrations. Unless you're clairvoyant. In which case, you probably don't need my advice in the first place haha.

## Testing migrations is a nightmare

You can't just run a migration and check if it works. You have to install an older version of your app, fill it with data that covers every field and model, then upgrade. On the simulator, a failed migration just corrupts the database, so you delete the app and start over - which tells you nothing about what real users will experience.

You essentially get one chance to catch issues: when you upload to TestFlight. If the migration fails there, you can't revert it. Your test data is corrupted. And if you used SwiftData with iCloud, now your iCloud data is corrupted too, so you have to figure out some way to manually delete what's in iCloud and what's local so you can hopefully get back to a state similar to what your actual users have. Just to verify that nothing broke.

Every time I push an update to the App Store, I cross my fingers and hope I didn't forget one of the million ways you can mess up SwiftData and accidentally make the app unusable for users.

## Corrupted data is unrecoverable

If a bad migration corrupts data on a user's device or in iCloud, the app crashes on launch. Forever. For that user.

You can't push an update that fixes it. The corrupted data is already there, sitting on their device, and there's no API to inspect it, clean it up, or recover it. There is no recovery path. The user is just stuck. The best advice you can give them is to delete and reinstall the app - and lose all their data.

## CloudKit makes everything worse

If you're using SwiftData with CloudKit for iCloud sync, things get significantly worse. Custom migrations aren't supported at all with CloudKit - you're locked into lightweight migrations only. You also have to manually deploy schema changes to the CloudKit production environment before every release. Forget, and sync silently breaks for all users.

I once forgot to push schema changes to the public CloudKit instance for about five months, causing upwards of 50% error rates. Users' data just wasn't syncing with iCloud. For months. And nothing in the app told me or my users that anything was wrong.

Even with everything set up correctly, CloudKit still shows 20-50% error rates, and there's no way to debug why people's data isn't syncing. I just have to shrug and hope it works itself out.

I've got iCloud sync set up and I have the app on my iPhone and Mac. Sometimes it works like magic: add an item on my phone, 30 seconds later it shows up on the Mac. Other times, I wait days and the data never shows up. There's no consistency and no way to diagnose the problem.

When you first download the app on a new device, if you have data in iCloud, it takes a couple hours for everything to sync. So it just looks like you've lost all your data. There's no way to tell the user "don't worry, your data is in iCloud, it's coming." You just have to wait and hope.

I've had users email me saying their data isn't syncing to their iPad. All I can do is respond and say "sorry, nothing I can do. Hope it works itself out."

There's also no "sync now" button to force a sync from iCloud. It's all "magic" and you just have to trust that it works. Except it often doesn't.

And there's no way to share SwiftData between users at all. Core Data had CloudKit features that let you share data between users. With SwiftData, it's simply not possible.

## The framework keeps changing under you

SwiftData's behavior changes between OS versions with little or no warning, and those changes can break existing apps.

**iOS 17 to 18:** Memory behavior changed - loading relationships started consuming 7x more RAM. AppIntents moved out-of-process. CloudKit sync behavior broke existing apps.

**iOS 18 to 26:** Data stores created on older OS versions have been reported to simply not load at all.

Migration behavior, field handling, and sync all change between OS versions. Error messages are cryptic and unhelpful, like "Fatal error: Expected only Arrays for Relationships." Good luck debugging that.

## The bottom line

SwiftData looks great in a WWDC demo. It feels great in a weekend project. But the moment you ship a real app to real users and start iterating on it, you run into walls that have no workarounds.

There's no reliable migration story. There's no recovery path for corrupted data. The sync layer can silently destroy your users' data. And the framework itself keeps changing in breaking ways beneath you.

After three years, my app has so much legacy code and technical debt that I can't get rid of because there's no migration path. I'm planning to switch to something like GRDB in the near future, because that's the only way I can actually clean things up.

If you're building something you plan to maintain, do yourself a favor: use SQLite, GRDB, or even Core Data. Anything with a real migration story and escape hatches. SwiftData doesn't have either.
