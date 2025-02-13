Certainly! Here’s the consolidated version to call a Python method from C++ using `pybind11`, with everything organized into three files:  

1. **C++ Class and Binding (`example.cpp`)**  
2. **Main Program (`main.cpp`)**  
3. **Python Script (`script.py`)**  

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
private:
    std::string name;
};

namespace py = pybind11;

// Define pybind11 module and bindings
PYBIND11_MODULE(example, m) {
    py::class_<MyClass>(m, "MyClass")
        .def(py::init<const std::string&>())
        .def("display", &MyClass::display)
        .def("get_name", &MyClass::getName);
}
```

---

### 2. Main Program (`main.cpp`):
```cpp
// main.cpp
#include <pybind11/embed.h> // Needed for embedding Python

namespace py = pybind11;
using namespace py::literals;

int main() {
    py::scoped_interpreter guard{}; // Start the Python interpreter

    // Import the C++ bindings and Python script
    py::module example_module = py::module::import("example");
    py::module script_module = py::module::import("script");

    // Create C++ object
    py::object cpp_obj = example_module.attr("MyClass")("TestObject");

    // Get the Python function and call it
    py::object python_method = script_module.attr("python_method");
    python_method(cpp_obj);

    return 0;
}
```

---

### 3. Python Script (`script.py`):
```python
# script.py

def python_method(obj):
    print("Inside Python Method")
    print("Name from C++ Object:", obj.get_name())
    obj.display()
```

---

### 4. Compile and Run:
Make sure `script.py` is in the same directory as the executable. Then, compile and run using:

```sh
c++ -O3 -Wall -shared -std=c++17 -fPIC \
    $(python3 -m pybind11 --includes) example.cpp \
    -o example$(python3-config --extension-suffix)

g++ main.cpp -o main -I/path/to/pybind11/include -lpython3.x
./main
```

---

### Key Points:
- **`example.cpp`**: Contains the C++ class and Pybind11 bindings, compiled as a Python module (`example.so`).
- **`main.cpp`**: Embeds the Python interpreter, imports the module and script, and calls the Python method.
- **`script.py`**: Holds the Python function that interacts with the C++ object.
- **`py::cast()`** is replaced with direct object creation using `example_module.attr("MyClass")()` for simplicity.

This setup keeps everything organized and functional as one unit.

