+++
title = "Integration Testing on Android against Firebase"
+++
I recently built out a solution for running Android integration tests against the 
[Firebase emulator][firebase-emulator] and there were a few rough edges so I thought
I'd document the process.

### The Problem

I've been (very slowly) building an Android app which uses [Firestore][firestore] and [Firebase Authentication][firebase-auth] because they give me an awful lot of functionality
for less work. Firestore gives you local-first, cross-platform storage for not much effort, and it's well integrated with Firebase Authentication.

I'm a big fan of decoupling the data layer from the business logic when architecting an app,
so I'd built a pretty straightforward abstraction layer for the data storage requirements. This isn't the actual code, but in essence it looks like this:

```kotlin
interface DocumentRepository {
	suspend fun getDocsForUser(user: User): Result<List<Document>, GetDocsError>
}
```

The `Result` there comes from [kotlin-result][kotlin-result], and it's a really decent cross-platform library for representing Result values. It's design is very reminiscent of the [Rust equivalent][rust-result], which is a big plus in my book, since I think Rust's stdlib is very well thought out.

Apart from that, there's not much to say. There's a concrete implementation of this interface `FirestoreDocumentRepository`  which actually contains all the code that talks to Firestore and all the business logic depends on the interface.

```kotlin
class FirestoreDocumentReposistory(
	private val firestore: Firestore
): DocumentRepository {

	override suspend fun getDocsForUser(user: User): Result<List<Document>, GetDocsError> {
		firestore.collection("users")
	}

}
```

### Integration Testing

So far, so good. We've got some nice decoupling, UI tests can run against fakes of the interface ([not mocks...][no-mocking]), and different builds of the app can use different implementations so we can make debug builds talk to the [Firebase emulator suite][firebase-emulator], for example.

But how do we make sure that `FirestoreDocumentRepository` is well tested? We need to use the Android Firebase libraries, but they only work on the Android emulator or a real device, not in a local JVM test with [Roboelectric][roboelectric]. 

At least, I haven't managed to get it to work and since Roboelectric stubs out a lot of the classes that the Firebase libraries require, and since the Firebase libraries are closed source, it would be a long, tiresome game of whack-a-mole finding out which classes need to not be stubbed out. And it would probably break frequently on new releases of the Firebase libraries.

That answers one half of the problem, but there's another: the backend. We obviously don't want test talking to the real Firestore database, since any bugs could cause affect user data. We _could_ set up a separate Firestore instance just to use for automated tests, but then our tests have to reach out the to the internet, making them flakier, and running too many tests could cost us real money.

I mentioned the [Firebase emulator suite][firebase-emulator] above. This is part of the [Firebase CLI tools][firebase-cli], and provides emulators for several Firebase products that run locally, precisely for testing. Yay! Problem solved!

### However...

There's a problem with this however. The integration tests have to run on an Android device/emulator, but the Firebase emulators aren't built to run in Android. They assume a desktop/server OS. So we'll need to start the emulator before running the integration tests, and pass the correct address/ports into the tests so they can connect to the emulator.

In order to make installing, starting and stopping the emulator as easy as possible, I built a [Docker image][firebase-emulator-docker], 

[firebase-emulator]: https://firebase.google.com/docs/emulator-suite
[firestore-emulator]: https://firebase.google.com/docs/emulator-suite/connect_firestore
[firebase-products]: https://firebase.google.com/products-build
[firestore]: https://firebase.google.com/products/firestore
[firebase-auth]: https://firebase.google.com/products/auth
[kotlin-result]: https://github.com/michaelbull/kotlin-result
[rust-result]: https://doc.rust-lang.org/std/result/index.html
[no-mocking]: https://testing.googleblog.com/2024/02/increase-test-fidelity-by-avoiding-mocks.html
[roboelectric]: https://robolectric.org/