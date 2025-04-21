# Function to generate -I include flags from compiler include directories
function(generate_include_flags output_variable)
    # Run g++ to get include paths
    execute_process(
        COMMAND g++ -E -x c++ - -v
        INPUT_FILE /dev/null
        OUTPUT_VARIABLE compiler_output
        ERROR_VARIABLE compiler_stderr
        RESULT_VARIABLE result
    )

    if(NOT result EQUAL 0)
        message(FATAL_ERROR "Failed to get include directories from g++")
    endif()

    # Split stderr lines
    string(REPLACE "\n" ";" compiler_lines "${compiler_stderr}")

    set(capturing FALSE)
    set(INCLUDE_FLAGS "")

    # Extract the paths from g++ stderr output
    foreach(line IN LISTS compiler_lines)
        string(STRIP "${line}" line)  # Remove leading/trailing spaces

        if("${line}" MATCHES "#include <...> search starts here:")
            set(capturing TRUE)
            continue()
        endif()

        if("${line}" MATCHES "End of search list.")
            set(capturing FALSE)
            continue()
        endif()

        if(capturing)
            list(APPEND INCLUDE_FLAGS "-I${line}")
        endif()
    endforeach()

    # Join flags into a space-separated string
    string(JOIN " " INCLUDE_FLAGS_STR ${INCLUDE_FLAGS})

    # Set the output variable
    set(${output_variable} "${INCLUDE_FLAGS_STR}" PARENT_SCOPE)
endfunction()

# Declare a variable to store the generated flags
set(INCLUDE_FLAGS "")

# Call the function to populate the variable with the include paths as -I flags
generate_include_flags(INCLUDE_FLAGS)

# Print the include flags
message(STATUS "Generated include flags: ${INCLUDE_FLAGS}")




