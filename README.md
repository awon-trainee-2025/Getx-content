## Table of Contents
1. [GetX Basics](#getx-basics)
2. [State Management](#state-management)
3. [Route Management](#route-management)
4. [Complete Example](#complete-example)

---

## GetX Basics
### Installation
Add to `pubspec.yaml`:
```yaml
dependencies:
  get: ^4.6.5
```

---

## State Management
### Reactive State Manager
Controller Class
```dart
class CounterController extends GetxController {
  var count = 0.obs; // Observable variable

  void increment() {
    count++;
    update(); // Notify listeners
  }
}
```
Usage in Widget
```dart
class CounterView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final CounterController controller = Get.put(CounterController());
    
    return Scaffold(
      appBar: AppBar(title: Text('GetX Counter')),
      body: Center(
        child: Obx(() => Text(
          'Count: ${controller.count.value}',
          style: TextStyle(fontSize: 24),
        )),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: controller.increment,
        child: Icon(Icons.add),
      ),
    );
  }
}
```
### Simple State Manager
Controller Class
```dart
class UserController extends GetxController {
  var user = User().obs;

  void updateName(String name) {
    user.update((val) {
      val?.name = name;
    });
  }
}
```
Usage in Widget
```dart
class UserProfile extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final UserController userController = Get.find();
    
    return Column(
      children: [
        Obx(() => Text('Name: ${userController.user.value.name}')),
        TextField(
          onChanged: userController.updateName,
        ),
      ],
    );
  }
}
```

---

## Route Management
### Navigation Basics
```dart
// Navigate to new screen
Get.to(() => NextScreen());

// Navigate and remove previous screen
Get.off(() => NextScreen());

// Navigate and remove all previous screens
Get.offAll(() => NextScreen());

// Go back
Get.back();

// Navigate with arguments
Get.to(() => DetailScreen(), arguments: {'id': 1});

// Get arguments in new screen
final args = Get.arguments;
```
### Named Routes
```dart
// Define routes
GetMaterialApp(
  initialRoute: '/',
  getPages: [
    GetPage(name: '/', page: () => HomeScreen()),
    GetPage(name: '/details', page: () => DetailScreen()),
    GetPage(
      name: '/profile',
      page: () => ProfileScreen(),
      transition: Transition.fadeIn,
    ),
  ],
);

// Navigate to named route
Get.toNamed('/details');

// Navigate with parameters
Get.toNamed('/profile?id=1&name=John');

// Get parameters
final id = Get.parameters['id'];
```

---

## Complete Example
### Todo App with GetX
1. Model
```dart
class Todo {
  int? id;
  String title;
  bool completed;

  Todo({this.id, required this.title, this.completed = false});
}
```
2. Controller
```dart
class TodoController extends GetxController {
  var todos = <Todo>[].obs;

  void addTodo(String title) {
    todos.add(Todo(
      id: todos.length,
      title: title,
      completed: false,
    ));
  }

  void toggleTodo(int id) {
    final index = todos.indexWhere((todo) => todo.id == id);
    todos[index].completed = !todos[index].completed;
    todos.refresh();
  }

  void deleteTodo(int id) {
    todos.removeWhere((todo) => todo.id == id);
  }
}
```
3. View
```dart
class TodoView extends StatelessWidget {
  final TodoController controller = Get.put(TodoController());
  final TextEditingController textController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('GetX Todo App')),
      body: Column(
        children: [
          Padding(
            padding: EdgeInsets.all(8.0),
            child: Row(
              children: [
                Expanded(
                  child: TextField(
                    controller: textController,
                    decoration: InputDecoration(labelText: 'New Todo'),
                  ),
                ),
                IconButton(
                  icon: Icon(Icons.add),
                  onPressed: () {
                    if (textController.text.isNotEmpty) {
                      controller.addTodo(textController.text);
                      textController.clear();
                    }
                  },
                ),
              ],
            ),
          ),
          Expanded(
            child: Obx(() => ListView.builder(
              itemCount: controller.todos.length,
              itemBuilder: (context, index) {
                final todo = controller.todos[index];
                return ListTile(
                  leading: Checkbox(
                    value: todo.completed,
                    onChanged: (_) => controller.toggleTodo(todo.id!),
                  ),
                  title: Text(todo.title),
                  trailing: IconButton(
                    icon: Icon(Icons.delete),
                    onPressed: () => controller.deleteTodo(todo.id!),
                  ),
                );
              },
            )),
          ),
        ],
      ),
    );
  }
}
```
4. Main app
```dart
void main() {
  runApp(GetMaterialApp(
    home: TodoView(),
    debugShowCheckedModeBanner: false,
  ));
}
```

## Folder Structure:
```
lib/
├── controllers/
├── views/
├── models/
├── bindings/
├── routes/
└── services/
```
