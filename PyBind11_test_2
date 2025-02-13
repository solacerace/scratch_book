Great idea! Let's enhance the example to print the changed name in the C++ code after it is modified in Python.

---

## Overview of Changes:
1. **Add Printing in C++ (`main.cpp`)** – After the Python script changes the name, the C++ code will print the updated name.
2. **Keep the Setter and Getter Integration** – The Python script still sets the new name.

---

### 1. C++ Class and Pybind11 Binding (`example.cpp`):
```cpp
// example.cpp
#include <pybind11/pybind11.h>
#include <string>
#include <iostream>

class MyClass {
public:
    MyClass(const std::string& name) : name(name) {}

    void display() const {
        std::cout << "C++ Object Name: " << name << std::endl;
    }

    std::string getName() const {
        return name;
    }

    void setName(const std::string& newName) {
        name = newName;
    }

private:
    std::string name;
};

namespace py = pybind11;

// Define pybind11 module and bindings
PYBIND11_MODULE(example, m) {
    py::class_<MyClass>(m, "MyClass")
        .def(py::init<const std::string&>())
        .def("display", &MyClass::display)
        .def("get_name", &MyClass::getName)
        .def("set_name", &MyClass::setName); // Expose the setter method
}
```

---

### 2. Updated Main Program (`main.cpp`):
```cpp
// main.cpp
#include <pybind11/embed.h> // Needed for embedding Python
#include <iostream>

namespace py = pybind11;
using namespace py::literals;

int main() {
    py::scoped_interpreter guard{}; // Start the Python interpreter

    // Import the compiled Python module and the script
    py::module example_module = py::module::import("example");
    py::module script_module = py::module::import("script");

    // Create C++ object via the Python binding
    py::object cpp_obj = example_module.attr("MyClass")("TestObject");

    // Call the Python method and pass the C++ object
    py::object python_method = script_module.attr("python_method");
    python_method(cpp_obj);

    // Get the updated name from C++ after Python changes it
    std::string updated_name = cpp_obj.attr("get_name")().cast<std::string>();
    std::cout << "Updated Name in C++ after Python change: " << updated_name << std::endl;

    return 0;
}
```

- **What's New?**:
  - After calling the Python method, C++ retrieves the updated name using `cpp_obj.attr("get_name")().cast<std::string>()`.
  - The updated name is printed in C++.

---

### 3. Python Script (`script.py`):
```python
# script.py

def python_method(obj):
    print("Inside Python Method")
    print("Name from C++ Object:", obj.get_name())
    obj.display()

    # Use the setter method to change the name
    obj.set_name("NewNameFromPython")
    print("Name changed in Python:")
    obj.display()
```

---

### 4. Compile `example.cpp` into `example.so`:
```sh
c++ -O3 -Wall -shared -std=c++17 -fPIC \
    $(python3 -m pybind11 --includes) example.cpp \
    -o example$(python3-config --extension-suffix)
```

---

### 5. Compile and Link `main.cpp`:
```sh
g++ main.cpp -o main \
    -I/path/to/pybind11/include \
    -L. -lpython3.x -Wl,-rpath,. -ldl
```

---

### 6. Ensure Files are in the Same Directory:
Ensure the following are in the same directory:
- `example.so` (Compiled Python module)
- `main` (Compiled executable)
- `script.py` (Python script)

---

### 7. Run the Program:
```sh
./main
```

---

### Expected Output:
```
Inside Python Method
Name from C++ Object: TestObject
C++ Object Name: TestObject
Name changed in Python:
C++ Object Name: NewNameFromPython
Updated Name in C++ after Python change: NewNameFromPython
```

---

### Key Points:
- **Bidirectional Data Flow**: Python changes the C++ object's state, and the change is reflected back in C++.
- **Consistent Integration**: `cpp_obj.attr("get_name")().cast<std::string>()` allows C++ to retrieve the updated value.
- **Cross-Language Consistency**: C++ and Python maintain a consistent view of the object state.

This example demonstrates seamless integration between C++ and Python, showing how changes made in Python can be observed in C++.

