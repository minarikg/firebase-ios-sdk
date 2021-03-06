# Unreleased

- [changed] Changed how the SDK handles locally-updated documents while syncing
  those updates with Cloud Firestore servers. This can lead to slight behavior
  changes and may affect the `SnapshotMetadata.hasPendingWrites` metadata flag.
- [changed] Eliminated superfluous update events for locally cached documents
  that are known to lag behind the server version. Instead, the SDK buffers
  these events until the client has caught up with the server.

# v0.14.0
- [fixed] Fixed compilation in C99 and C++11 modes without GNU extensions.

# v0.13.6
- [changed] Internal improvements.

# v0.13.5
- [changed] Some SDK errors that represent common mistakes (such as permission
  denied or a missing index) will automatically be logged as a warning in
  addition to being surfaced via the API.

# v0.13.4
- [fixed] Fixed an issue where the first `get()` call made after being offline
  could incorrectly return cached data without attempting to reach the backend.
- [changed] Changed `get()` to only make one attempt to reach the backend before
  returning cached data, potentially reducing delays while offline.
- [fixed] Fixed an issue that caused Firebase to drop empty objects from calls
  to `setData(..., merge:true)`.

# v0.13.3
- [changed] Internal improvements.

# v0.13.2
- [fixed] Fixed an issue where changes to custom authentication claims did not
  take effect until you did a full sign-out and sign-in. (#1499)
- [changed] Improved how Firestore handles idle queries to reduce the cost of
  re-listening within 30 minutes.

# v0.13.1
- [fixed] Fixed an issue where `get(source:.Cache)` could throw an
  "unrecognized selector" error if the SDK has previously cached the
  non-existence of the document (#1632).

# v0.13.0
- [feature] Added `FieldValue.arrayUnion()` and `FieldValue.arrayRemove()` to
  atomically add and remove elements from an array field in a document.
- [feature] Added `whereField(arrayContains:)` query filter to find
  documents where an array field contains a specific element.
- [fixed] Fixed compilation with older Xcode versions (#1517).
- [fixed] Fixed a performance issue where large write batches with hundreds of
  changes would take a long time to read and write and consume excessive memory.
  Large write batches should now see no penalty.
- [fixed] Fixed a performance issue where adding a listener for a large
  (thousands of documents) collection would take a long time in offline mode
  (#1477).
- [fixed] Fixed an issue that could cause deleted documents to momentarily
  re-appear in the results of a listener, causing a flicker (#1591).

# v0.12.6
- [fixed] Fixed an issue where queries returned fewer results than they should,
  caused by documents that were cached as deleted when they should not have
  been (#1548). Some cache data is cleared and so clients may use extra
  bandwidth the first time they launch with this version of the SDK.

# v0.12.5
- [changed] Internal improvements.

# v0.12.4
- [fixed] `setData` methods taking `mergeFields:` arguments can now delete
  fields using `FieldValue.delete()`.
- [fixed] Firestore will now recover from auth token expiration when the system
  clock is wrong.
- [fixed] Fixed compilation with older Xcode versions (#1366).

# v0.12.3
- [changed] Internal improvements.

# v0.12.2
- [fixed] Fixed an issue where `FirestoreSettings` would accept a concurrent
  dispatch queue, but this configuration would trigger an assertion failure.
  Passing a concurrent dispatch queue should now work correctly (#988).

# v0.12.1
- [changed] Internal improvements.

# v0.12.0
- [changed] Replaced the `DocumentListenOptions` object with a simple boolean.
  Instead of calling
  `addSnapshotListener(options: DocumentListenOptions.includeMetadataChanges(true))`
  call `addSnapshotListener(includeMetadataChanges:true)`.
- [changed] Replaced the `QueryListenOptions` object with simple booleans.
  Instead of calling
  `addSnapshotListener(options:
      QueryListenOptions.includeQueryMetadataChanges(true)
          .includeDocumentMetadataChanges(true))`
  call `addSnapshotListener(includeMetadataChanges:true)`.
- [changed] `QuerySnapshot.documentChanges()` is now a method which optionally
  takes `includeMetadataChanges:true`. By default even when listening to a
  query with `includeMetadataChanges:true` metadata-only document changes are
  suppressed in `documentChanges()`.
- [changed] Replaced the `SetOptions` object with a simple boolean. Instead of
  calling `setData(["a": "b"], options: SetOptions.merge())` call
  `setData(["a": "b"], merge: true)`.
- [changed] Replaced the `SnapshotOptions` object with direct use of the
  `FIRServerTimestampBehavior` on `DocumentSnapshot`. Instead of calling
  `data(SnapshotOptions.serverTimestampBehavior(.estimate))` call
  `data(serverTimestampBehavior: .estimate)`. Changed `get` similarly.
- [changed] Added ability to control whether DocumentReference.getDocument() and
  Query.getDocuments() should fetch from server only, cache only, or attempt
  server and fall back to the cache (which was the only option previously, and
  is now the default.)
- [feature] Added new `mergeFields:(NSArray<id>*)` override for `set()`
  which allows merging of a reduced subset of fields.

# v0.11.0
- [fixed] Fixed a regression in the Firebase iOS SDK release 4.11.0 that could
  cause `getDocument()` requests made while offline to be delayed by up to 10
  seconds (rather than returning from cache immediately).
- [feature] Added a new `Timestamp` class to represent timestamp fields,
  currently supporting up to microsecond precision. It can be passed to API
  methods anywhere a system Date is currently accepted. To make
  `DocumentSnapshot`s read timestamp fields back as `Timestamp`s instead of
  Dates, you can set the newly added property `areTimestampsInSnapshotsEnabled`
  in `FirestoreSettings` to `true`. Note that the current behavior
  (`DocumentSnapshot`s returning system Dates) will be removed in a future
  release. Using `Timestamp`s avoids rounding errors (system Date is stored as
  a floating-point value, so the value read back from a `DocumentSnapshot`
  might be slightly different from the value written).

# v0.10.4
- [changed] If the SDK's attempt to connect to the Cloud Firestore backend
  neither succeeds nor fails within 10 seconds, the SDK will consider itself
  "offline", causing getDocument() calls to resolve with cached results, rather
  than continuing to wait.
- [fixed] Fixed a race condition after calling `enableNetwork()` that could
  result in a "Mutation batchIDs must be acknowledged in order" assertion crash.
- [fixed] Fixed undefined symbols in the absl namespace (#898).

# v0.10.3
- [fixed] Fixed a regression in the 4.10.0 Firebase iOS SDK release that
  prevented the SDK from communicating with the backend before successfully
  authenticating via Firebase Authentication or after unauthenticating and
  re-authenticating. Reads and writes would silently be executed locally
  but not sent to the backend.

# v0.10.2
- [changed] When you delete a FirebaseApp, the associated Firestore instances
  are now also deleted (#683).
- [fixed] Fixed race conditions in streams that could be exposed by rapidly
  toggling the network from enabled to disabled and back (#772) or encountering
  a failure from the server (#835).
- [fixed] Addressed warnings shown by the latest versions of Xcode and CocoaPods.

# v0.10.1
- [fixed] Fixed a regression in Firebase iOS release 4.8.1 that could in certain
  cases result in an "OnlineState should not affect limbo documents." assertion
  crash when the client loses its network connection.
- [fixed] It's now possible to pass a nil completion block to WriteBatch.commit (#745).

# v0.10.0
- [changed] Removed the includeMetadataChanges property in FIRDocumentListenOptions
  to avoid confusion with the factory method of the same name.
- [changed] Added a commit method that takes no completion handler to FIRWriteBatch.
- [feature] Queries can now be created from an NSPredicate.
- [feature] Added SnapshotOptions API to control how DocumentSnapshots return unresolved
  server timestamps.
- [feature] Added `disableNetwork()` and `enableNetwork()` methods to
  `Firestore` class, allowing for explicit network management.
- [changed] For non-existing documents, DocumentSnapshot.data() now returns `nil`
  instead of throwing an exception. A non-nullable QueryDocumentSnapshot is
  introduced for Queries to reduce the number of nil-checks in your code.
- [changed] Snapshot listeners (with the `includeMetadataChanges` option
  enabled) now receive an event with `snapshot.metadata.isFromCache` set to
  `true` if the SDK loses its connection to the backend. A new event with
  `snapshot.metadata.isFromCache` set to false will be raised once the
  connection is restored and the query is in sync with the backend again.
- [fixed] Multiple offline mutations now properly reflected in retrieved
  documents. Previously, only the last mutation would be visible. (#643)
- [fixed] Fixed a crash in `closeWithFinaleState:` that could be triggered by
  signing out when the app didn't have a network connection.

# v0.9.4
- [changed] Firestore no longer has a direct dependency on FirebaseAuth.
- [fixed] Fixed a crash when using path names with international characters
  with persistence enabled.
- [fixed] Addressed race condition during the teardown of idle streams (#490).

# v0.9.3
- [changed] Improved performance loading documents matching a query.
- [changed] Cleanly shut down idle write streams.

# v0.9.2
- [changed] Firestore now retries requests more often before considering a client offline.
- [changed] You can now use FieldValue.delete() with SetOptions.merge().

# v0.9.1
- [fixed] Fixed validation of nested arrays to allow indirect nesting.

# v0.9.0
- [fixed] Add an NS_SWIFT_NAME for FIRSnapshotMetadata and FIRListenerRegistration.
- [fixed] Fixed retain cycle in DocumentReference.getDocument(completion:).

# v0.8.0
- Initial public release.
