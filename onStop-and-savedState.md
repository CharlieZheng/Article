- https://developer.android.com/topic/libraries/architecture/lifecycle.html#onStop-and-savedState

When a Fragment or AppCompatActivity's state is saved via onSaveInstanceState(), it's UI is considered immutable until ON_START is called. Trying to modify the UI after the state is saved is likely to cause inconsistencies in the navigation state of your application which is why FragmentManager throws an exception if the app runs a FragmentTransaction after state is saved.
