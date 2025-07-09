# simple-MVVM-app
Simple app that let you learn MVVM with ease

🔁 MVVM Breakdown
1. Model Layer

   data class CounterModel(var count : Int)

CounterModel is a data class that holds a single property: count.
Represents the data state of the app (i.e., the counter value).

    class CounterRepository {

        fun getCounter() = _counter

        fun incrementCounter() {
            _counter.count++
        }

        fun decrementCounter() {
            _counter.count--
        }
    }

CounterRepository simulates a data layer (like a database or network).

Provides methods to access and update the counter.

Manages business logic for modifying the model.

2. ViewModel Layer
class CounterViewModel : ViewModel() {
    private val _repository: CounterRepository = CounterRepository()
    private val _count = mutableStateOf(_repository.getCounter().count)

    val count: MutableState<Int> = _count

CounterViewModel acts as the bridge between UI and data.
Uses mutableStateOf(...) to expose count as a reactive state observable by the UI.
Pulls data from CounterRepository and notifies UI of changes.

    fun increment() {
        _repository.incrementCounter()
        _count.value = _repository.getCounter().count
    }

        fun decrement() {
            _repository.decrementCounter()
            _count.value = _repository.getCounter().count
        }
    }
Methods update the repository and refresh the observed state.

This triggers recomposition of the UI when count changes.

3. View Layer (Activity & Composables)
MainActivity

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        ...
        val viewModel: CounterViewModel = viewModel()
        ...
        TheCounterApp(viewModel)
    }
}

Entry point of the app.
Injects the CounterViewModel using Jetpack's viewModel() delegate.
Sets the UI content using Jetpack Compose.

TheCounterApp
@Composable
fun TheCounterApp(viewModel: CounterViewModel) {
    ...
    Text(text = "Count: ${viewModel.count.value}")
    ...
    Button(onClick = { viewModel.increment() }) { Text("Increment") }
    Button(onClick = { viewModel.decrement() }) { Text("Decrement") }
}

Reactive Composable that displays the current count.
Re-renders automatically when viewModel.count.value changes.
Buttons call the ViewModel's logic.

PreviewApp
@Composable
@Preview
fun PreviewApp() {
    val viewModel: CounterViewModel = viewModel()
    TheCounterApp(viewModel)
}
Used in Android Studio’s preview panel.

✅ Summary: Flow of Data
User taps a button.

UI → ViewModel.increment() or .decrement()

ViewModel updates the Repository

ViewModel pulls new value from Repository and updates count state

UI observes count and recomposes

✅ Good Practices Followed
Clean separation between Model, ViewModel, and View.

Using Jetpack Compose’s mutableStateOf for state-driven UI.

Leveraging viewModel() for lifecycle-aware ViewModel creation.
