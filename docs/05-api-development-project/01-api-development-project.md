# API Development Project

[Take me to the lab!](https://kodekloud.com/topic/lab-api-developement-project/)

Help for the [VSCode editor](https://github.com/kodekloudhub/community-faq/blob/main/docs/vscode-tips.md).

This is the big one - the test of everything you have learned so far! There's a lot to do in the one hour you have to complete the lab.

This API project represents a simple CRUD (Create, Read, Update, Delete) API for simple task data, and we are using a test-driven-development (TDD) methodology. This means that the tests are already written, and the code must be written to satisfy the tests.

* [Getting Started](#getting-started)
* [The model](#the-model)
    * [func (t *Task) getTask() error](#func-t-task-gettask-error)
    * [func (t *Task) updateTask() error](#func-t-task-updatetask-error)
    * [func (t *Task) deleteTask() error](#func-t-task-deletetask-error)
* [The application](#the-application)
    * [func sendResponse(w http.ResponseWriter, statusCode int, payload interface{})](#func-sendresponsew-httpresponsewriter-statuscode-int-payload-interface)
    * [func sendError(w http.ResponseWriter, statusCode int, err string)](#func-senderrorw-httpresponsewriter-statuscode-int-err-string)
    * [func (app *App) getTasks(writer http.ResponseWriter, request *http.Request)](#func-app-app-gettaskswriter-httpresponsewriter-request-httprequest)
    * [func (app *App) createTask(writer http.ResponseWriter, r *http.Request)](#func-app-app-createtaskwriter-httpresponsewriter-r-httprequest)
    * [func (app *App) readTask(writer http.ResponseWriter, request *http.Request)](#func-app-app-readtaskwriter-httpresponsewriter-request-httprequest)
    * [func (app *App) updateTask(writer http.ResponseWriter, request *http.Request)](#func-app-app-updatetaskwriter-httpresponsewriter-request-httprequest)
    * [func (app *App) deleteTask(writer http.ResponseWriter, request *http.Request)](#func-app-app-deletetaskwriter-httpresponsewriter-request-httprequest)
* [And finally...](#and-finally)

# Getting Started

We have been given three code files. What we know is that the tests in `main_test.go` are complete, so we need to reverse engineer from the tests what it is that the code in `main.go` and `model.go` should do.

In these code files, we have been given the following

* The `Task` struct (`main.go` line 10)
* The router setup which is the part that routes the incoming URL to the correct handler function (`main.go` line 24)
* The code to set up our initial database of tasks, create the HTTP server and begin listening for connections (`main.go` lines 38-47)
* The implementation of the initial task database (`model.go` line 3)
* The implementation of adding a task to the task database (`model.go` line 19)

We need to add code to do the following

* In `model.go`
    * Return all the tasks in the task database (`func getTasks`)
    * Return a single task (`func getTask`)
    * Update a task (`func updateTask`)
    * Delete a task (`func deleteTask`)
* In `main.go`
    * Return a response to the caller (`func sendResponse`)
    * Return an error to the caller (`func sendError`)
    * Get the complete list of tasks (`func getTasks`)
    * Add a task (`func createTask`)
    * Get a single task by ID (`func readTask`)
    * Update a task by ID (`func updateTask`)
    * Delete a task by ID (`func DeleteTask`)

The five operations related to `Task` in `main.go` are the CRUD operations. Each will call the appropriate function in `model.go` and return content to the caller using either `sendResponse` or `sendError` according to what happens during processing.

I use the term "task database" since in a real application, the tasks would indeed be persisted to a real database like MySQL. However in this little project, the database is in-memory, represented by a slice of `Task`.

We can see from the tests that the payloads sent and returned across the API are going to be JSON data, by the presence of `json.Marshal` and `json.Unmarshal` calls. It's usual for REST APIs to exchange data as JSON, therefore we are certainly going to need to add an import for that in `main.go`

```go
import (
    "encoding/json"  // <- add this
    "log"
    "net/http"

    "github.com/gorilla/mux"
)
```

# The model

Let's begin by fleshing out the model, filling out the methods that manipulate the task list.

Note the two package scope variables declared in `main.go`

```go
var tasks []Task
var currentID int
```

These are the current task list (the task database), and the ID of the last task that was created. Examine `CreateIntialTasks` in `model.go`, and then `func main` and `func (app *App) Initialise` in `main.go` for how they get set up.

## func (t *Task) getTask() error

This is a pointer receiver on `Task` struct. We can assume that to get a task we will need an ID. Since there's no `id` argument, then the id is going to be set on `t *task` (which we will see later, but for now assume this is the case).

We also have an `error` return, which implies that if the given task ID can't be found we need to return a non-nil error.

So the logic for this function is to search the task list for the given ID. If the task is found in the list, update the fields of `t *task` with what was found. If the task isn't found return an error. We need to refer to the test code to find out what the text of the error should be. Look at the code following line 80 in `main_test.go`. We can see that the expected error text is "task not found", so that must be returned as the error object if we don't find the task.

```go
func (t *Task) getTask() error {
    // Get the task ID from the receiver argument
    id := t.ID

    // Range over existing tasks
    for _, task := range tasks {
        if task.ID == id {
            // Matching ID - fill in other fields of *t
            t.DueDate = task.DueDate
            t.Name = task.Name
            t.Description = task.Description
            // return no error
            return nil
        }
    }

    // If we get here, the task isn't found
    // so return the correct error text as an error object.
    return errors.New("task not found")
}
```

## func (t *Task) updateTask() error

Again, a pointer receiver. Because we are updating, we will assume all the fields of `*t` have values, and those values we will set in the task list if we find the task.

Refer to the test for updating to find the correct error message. There doesn't appear to be an explicit test for the wrong task ID, so let's assume the message should still be "task not found" as that would be consistent.

The code for this function is very similar to the `getTask`, function in that we must loop the task list looking for the ID, but we will *set* rather than *get* the values in the task list if the task was found.

```go
func (t *Task) updateTask() error {
    // Get the task ID from the receiver argument
    id := t.ID

    // Range over existing tasks, capturing the index as well as the task content
    for index, task := range tasks {
        if task.ID == id {
            // Found task - update details
            task.DueDate = t.DueDate
            task.Name = t.Name
            task.Description = t.Description
            // Set updated task back into the list at the index position.
            tasks[index] = task

            // no error
            return nil
        }
    }

    // If we get here, the task isn't found
    // so return the correct error text as an error object.
    return errors.New("task not found")
}
```

## func (t *Task) deleteTask() error

Once again, the ID of the task will be given in `t *Task`. We will need to loop through the tasks to find the slice index of the task with the given ID and return a "task not found" error (`main_test.go` line 206) if the task ID isn't present. To actually remove the task from the list, we will use some slice manipulation.

```go
func (t *Task) deleteTask() error {
    // Get the task ID from the receiver argument
    id := t.ID

    // Initialise this to -1 to indicate not found yet
    indexToBeDeleted := -1

    // Range across the task list checking task ID
    for index, task := range tasks {
        if task.ID == id {
            // Found the task, store index
            indexToBeDeleted = index

            // We should break here, but that's not in the
            // solution tab of the lab.
            // If you do put break here, the tests will still pass
            // and the lab will still complete :-)
        }
    }

    if indexToBeDeleted == -1 {
        // if task.ID == id was never true, then task not found
        return errors.New("task not found")
    }

    // Shuffle the task slice to remove the one to delete by appending
    // a slice of everything up to but not including the index to delete
    // to a slice of everything _after_ the index to delete.
    tasks = append(tasks[:indexToBeDeleted], tasks[indexToBeDeleted+1:]...)

    // No error
    return nil
}
```

# The Application

Because this project is built on the `net/http` and `gorilla/mux` packages, most of the heavy lifting of managing HTTP connections is done for us including creating the HTTP server itself and handling some common errors like invalid URLs that don't match any defined route, allowing us to focus on the application logic. The router wires up HTTP methods to function calls, and provides as function arguments `http.ResponseWriter` to send data back to the caller, and `*http.Request` to permit receiving data from the caller, such as the ID of a task to operate on and any JSON payload sent by the caller. `gorilla/mux` also provides methods for getting the data we need, such as the task ID from the URL requested by the caller, so we will be leveraging those.

Know also the meanings of HTTP status codes when we want to report application logic success or errors to the API's caller. The `http` package exports all the [status codes](https://pkg.go.dev/net/http@go1.19.0#pkg-constants) as convenient `int` constants. Know that codes in the 200-299 range indicate success, 400-499 indicates that the caller did something wrong (inc, 404 NotFound meaning the caller requested a resource that isn't present), and 500-599 indicate an error in the server (our code), i.e. the caller request is valid but the server couldn't process it.

Let's first pick off some of the simpler boilerplate that's going to be used by the other functions we have to complete.

## func sendResponse(w http.ResponseWriter, statusCode int, payload interface{})

This looks like it's going to be used whenever the API is going to send some data back to the caller. We have 3 function arguments

| Argument                | What it's for                                                                              |
|-------------------------|--------------------------------------------------------------------------------------------|
| `w http.ResponseWriter` | This is a writer object that will write data back over the connection to the API's caller. |
| `statusCode int`        | This will be the HTTP code, e.g. 200 (OK), 404 (Not Found) etc.                            |
| `payload interface{}`   | Any data that can reasonably be converted to JSON (structs, maps, arrays/slices)           |

This function therefore needs to do the following

1. Marshal the payload data to JSON.
1. Set the correct response header (`Content-Type`) that indicates we are sending JSON (`application/json`).
1. Set the status code
1. Write the JSON into the writer.

[Documentation](https://pkg.go.dev/net/http@go1.19.0#ResponseWriter) for `ResponseWriter`

Turning the above 4 steps to code, we have

```go
func sendResponse(w http.ResponseWriter, statusCode int, payload interface{}) {
    response, _ := json.Marshal(payload)
    w.Header().Set("Content-type", "application/json")
    w.WriteHeader(statusCode)
    w.Write(response)
}
```

## func sendError(w http.ResponseWriter, statusCode int, err string)

Returning an error to the caller implies sending a response. An error is simply a response with an HTTP status code >= 400.

To see how we need to form the error payload, we must refer to the test code. Look at line 85 in `main_test.go`. You can see this

```go
    var errorMessage map[string]string                      // line 85
    json.Unmarshal(response.Body.Bytes(), &errorMessage)

    if errorMessage["error"] != "task not found" {
        t.Errorf("handler returned unexpected error message: got %v want %v", errorMessage["error"], "task not found")
    }
```

This tells us that errors are returned as a `map[string]string`, therefore this is what the `sendError` function must prepare. We can also see that the map key is `error`.

We have 3 function arguments

| Argument                | What it's for                                                                              |
|-------------------------|--------------------------------------------------------------------------------------------|
| `w http.ResponseWriter` | This is a writer object that will write data back over the connection to the API's caller. |
| `statusCode int`        | This will be the HTTP code, e.g. 200 (OK), 202 (Accepted) etc.                             |
| `err string`            | The text of the error message.                                                             |

So the task here is straight forward. Create a `map[string]string` for the error content, and pass it to `sendResponse` along with the status code, for delivery to the API caller.

```go
func sendError(w http.ResponseWriter, statusCode int, err string) {
    errorMessage := map[string]string{"error": err}
    sendResponse(w, statusCode, errorMessage)
}
```

Now lets fill out the handler methods for the API's routes....


## func (app *App) getTasks(writer http.ResponseWriter, request *http.Request)

As is convention, if a handler function has a plural name, it will return multiple results, and these will be a JSON list. In this case, it should return the entire content of the `Tasks` slice.

Look at the test `TestGetAllTasks`. It's doing the following

1. Calling the model's `getTasks` function to get the list of tasks it expects to come back from the API call
1. Makes the API call and gathers the response
1. Checks that the response code is 200 (OK)
1. Unmarshals the JSON data into a separate slice of tasks
1. Compares each task it got from the API with each task it got by the direct route to the model.

So we need to do this in our implementation:

1. Call the `getTasks` function in `model.go` to get all the tasks.
1. If that returns an error, we need to send the error using `sendError`
1. Else we need to send the response, which is the list of tasks we got from `getTasks`

```go
func (app *App) getTasks(writer http.ResponseWriter, request *http.Request) {
    // Get all tasks from the model
    tasks, err := getTasks()
    if err != nil {
        // It returned an error - send it to caller with a 500 status code
        sendError(writer, http.StatusInternalServerError, err.Error())
        return
    }

    // Send the task list with a 200 (OK) status.
    sendResponse(writer, http.StatusOK, tasks)
}
```

## func (app *App) createTask(writer http.ResponseWriter, r *http.Request)

Here we are going to add a new task. The implementation of `createTask` is already provided in the model. In CRUD APIs like this one, a create operation is implemented as HTTP POST, and the data to add comes as a JSON payload on the `*http.Request` object.

Look at `TestCreateTask`. It has two cases inside. It's going to test with a valid task which it will expect to be created, and test with an invalid task and check for the expected error.

Looking at the first case, the test does this

1. Create a task object with valid data. Note we *don't* need to give the task an ID. The `createTask` method will assign this using the value of `currentID`.
1. Marshal the task data to JSON
1. Create a POST request object, adding the JSON data payload
1. Make the call using `sendRequest`
1. Check that the server (our handler function) returns a 201 (Created) status
1. Next it validates the created task, which is expected to be returned from our create handler.

Now the error case - the test does this

1. Creates a POST request with an invalid payload - it's a string not a task.
1. Sends the request
1. Checks that the returned status is 400 (BadRequest)
1. Unmarshals the error message from the response and checks the error string is correct.

This means that our handler function needs to do the following

1. Attempt to decode the JSON in the `*http.Request` as a `Task` struct
1. If this fails, then the payload is invalid, so we need to return status 400 (BadRequest), and the expected error message "Invalid request payload"
1. If we get past this check, then we call the `createTask` method in model to add the new task
1. If `createTask` returns an error, we treat this as a server error 500 (InternalServerError), and return it along with the error itself
1. If `createTask` did not error, we return the updated `Task` struct which now has an ID set to the caller.

```go
func (app *App) createTask(writer http.ResponseWriter, r *http.Request) {
    // Declare an instance of Task into which we will
    // attempt to read the data sent by the caller's request.
    var p Task

    // Use a json.Decoder to decode the []byte data in the response and
    // store the result in `p` - the instance of a task struct
    err := json.NewDecoder(r.Body).Decode(&p)
    if err != nil {
        // The payload did not contain something
        // that looks like the correct JSON for a task.
        // That's a user error sending the wrong data.
        sendError(writer, http.StatusBadRequest, "Invalid request payload")
        return
    }

    // Create the task, which will set p.ID (it's a pointer receiver)
    // with a new, unused ID.
    err = p.createTask()
    if err != nil {
        // Task could not be created - that's a server error
        sendError(writer, http.StatusInternalServerError, err.Error())
        return
    }

    // Send the updated task info back to the caller
    // with a status of Created, because that's what we did!
    sendResponse(writer, http.StatusCreated, p)
}
```

## func (app *App) readTask(writer http.ResponseWriter, request *http.Request)

This handler method is going to read a single task by calling `getTask` that we have implemented in `model.go`. As is convention in REST, the task ID to get will be passed as part of the URL, e.g. `http://example.com/task/1` and the request method is GET. If you look at the router setup provided in `main.go` we see this line

```go
    app.Router.HandleFunc("/task/{id}", app.readTask).Methods("GET")
```

Gorilla Mux will capture the id for us, and the package provides [a Vars method](https://pkg.go.dev/github.com/gorilla/mux#Vars) for us to retrieve the value of this parameter (and any other URL parameters that may have been requested). The `Vars` call will return a `map[string]string` where the key is `id` and the value is the ID we want *as a string* so we will need to deal with that! Recall that the `strconv` package contains a method `Atoi` which will convert the string representation of an integer to an `int`. At this point, we'll need to add `strconv` to the list of imports in `main.go`

Ok, now lets see what the test for this method expects...

There's 3 cases in the test for this method

1. Valid task ID (of `1`). It will expect our method to return task  with ID = 1 and a 200 (OK) status code. Note that the test is going to compare the result with the actual `Tasks[0]`, which, since we haven't deleted any tasks yet, should be the task with ID = 1.
1. Invalid task ID. It will expect a status code of 404 (NotFound) and error text of "task not found". We will get that error message from the previous implementation of `getTask` in `model.go`, however the status code will need to be set by this function.
1. Invalid task ID format. It will send `abc` as the task ID and expect a status code of 400 (BadRequest) and an error message "invalid task ID". Trying to convert a non integer value with `Atoi` will return an error. We can handle this error and satisfy the test condition by returning what it expects.

So, this function needs to do the following:

1. Read the URL parameter `id` using `mux.Vars`
1. Try to convert this to an `int`
1. If that fails, satisfy test case 3 above by sending the correct error.
1. Create a `Task` struct with the ID we have received
1. Call `getTask` in the model to retrieve the task data
1. If an error is retuned, it will be because no task with that ID exists (check implementation of `getTask` above). Return that error with a 404 (NotFound) status
1. If we get here, then send the task data as a response.

```go
import (
    "encoding/json"
    "log"
    "net/http"
    "strconv"       // <- add this
    "github.com/gorilla/mux"
)

```

```go
func (app *App) readTask(writer http.ResponseWriter, request *http.Request) {
    // Get the URL parameters
    // vars will be map[string]string
    vars := mux.Vars(request)

    // Try to get an integer from the "id" argument as "key"
    key, err := strconv.Atoi(vars["id"])
    if err != nil {
        // If that fails (like it's eg. "abc"), then send 400 (BadRequest)
        // and message "invalid task ID" to the caller
        sendError(writer, http.StatusBadRequest, "invalid task ID")
        return
    }

    // Create a Task struct with only the ID we received
    t := Task{ID: key}

    // Call the getTask receiver that will either fill in the task
    // details or return an error because the task with that ID isn't found
    err = t.getTask()

    if err != nil {
        // We got an error.
        // Send a 404 (NotFound) status and the text of the
        // error returned by getTask
        sendError(writer, http.StatusNotFound, err.Error())
        return
    }

    // All good. Send the filled-in task to the caller.
    sendResponse(writer, http.StatusOK, t)
}
```

## func (app *App) updateTask(writer http.ResponseWriter, request *http.Request)

This handler method updates a given task passed by ID with the new task data passed as JSON in the request object using the request method PUT. As with `readTask`, we must obtain the ID from the URL parameters.

Let's again examine the test to see what it does.

There are 3 cases in the test

1. Update task with ID = 1 to set task name to "Collect Requirements". PUT the update and verify the status code is 200 (OK), receive the updated task back from the API and verify all fields are as expected.
1. Resend the same payload as was used for case 1, but with an invalid task ID ("abc"). Verify that the status code is 400 (BadRequest) and the error message is "Invalid task ID". Note this is the same response as for `readTask` in this situation.
1. Test with invalid payload (a string rather than a task). Verify the status code is 400 (BadRequest) and the error message is "Invalid request payload". Note this is the same response as `createTask` in this situation.

So, this function needs to do the following:

1. Read the URL parameter `id` using `mux.Vars`
1. Try to convert this to an `int`
1. If that fails, satisfy test case 2 above by sending the correct error.
1. Try to decode the request payload as a `Task` struct.
1. If that fails, satisfy test case 3 above by sending the correct error.
1. Set task ID on decoded task with ID got from the URL parameters and call `updateTask` in the model.
1. If that fails return status 500 (InternalServerError) and error message returned from `updateTask` in the model.
1. Return the updated task to the caller with a 200 (OK) status

```go
func (app *App) updateTask(writer http.ResponseWriter, request *http.Request) {
    // Get the URL parameters
    // vars will be map[string]string
    vars := mux.Vars(request)

    // Try to get an integer from the "id" argument as "key"
    key, err := strconv.Atoi(vars["id"])
    if err != nil {
        // If that fails (like it's eg. "abc"), then send 400 (BadRequest)
        // and message "invalid task ID" to the caller
        sendError(writer, http.StatusBadRequest, "invalid task ID")
        return
    }

    // Declare instance of Task struct
    var t Task

    // Try to decode the caller's JSON into the task struct
    err = json.NewDecoder(request.Body).Decode(&t)

    if err != nil {
        // Failed - not a correct Task payload,
        // send expected error details
        sendError(writer, http.StatusBadRequest, "Invalid request payload")
        return
    }

    // Set task's ID from URL parameter
    t.ID = key

    // Cal the model's implementation to do the work
    err = t.updateTask()

    if err != nil {
        // Model returned error - send it to caller with 500 (InternalServerError)
        sendError(writer, http.StatusInternalServerError, err.Error())
        return
    }

    // Return edited task to caller
    sendResponse(writer, http.StatusOK, t)
}
```

## func (app *App) deleteTask(writer http.ResponseWriter, request *http.Request)

This handler method deletes a given task passed by ID using the request method DELETE. As with `readTask`, we must obtain the ID from the URL parameters.

Let's check the test code. There are two test cases:

1. A valid task ID (3) is provided. The test expects to receive a 200 (OK) status and a payload of `map[string]interface{}` containing `map[result:successful deletion]`
1. An invalid task ID is given. The test expects to receive a 404 (NotFound) status and an error message of "task not found".

Note that there isn't a test case for invalid task ID (e.g. `abc`), but in the interests of completeness and avoidance of bugs if this were a real application, the function should still check for this. What we actually have is an incomplete test!

So, this function should do the following

1. Read the URL parameter `id` using `mux.Vars`
1. Try to convert this to an `int`
1. If that fails, send the invalid task ID error as we have in other functions.
1. Create an empty `Task` with the given ID to use with the `deleteTask` receiver in the model, and call it.
1. If there was an error, it will be because the given task ID doesn't exist (refer your implementation of this receiver above). Send the expected error details
1. Send as a response, the expected success message and a 200 (OK) status.

```go
func (app *App) deleteTask(writer http.ResponseWriter, request *http.Request) {
    // Get the URL parameters
    // vars will be map[string]string
    vars := mux.Vars(request)

    // Try to get an integer from the "id" argument as "key"
    key, err := strconv.Atoi(vars["id"])

    if err != nil {
        // If that fails (like it's eg. "abc"), then send 400 (BadRequest)
        // and message "invalid task ID" to the caller
        sendError(writer, http.StatusBadRequest, "invalid task ID")
        return
    }

    // Initialise a Task struct with the given ID
    t := Task{ID: key}

    // Call the deleteTask receiver
    err = t.deleteTask()

    if err != nil {
        // Failed - not not found,
        // send expected error details
        sendError(writer, http.StatusNotFound, err.Error())
        return
    }

    // Send a map with the expected key and value to the caller along with 200 (OK) status.
    // Recall that sendResponse takes interface{} as its payload argument,
    // which means we can send almost anything and it will be converted to JSON.
    // We are calling it here in the same way that sendError calls it.
    sendResponse(writer, http.StatusOK, map[string]string{"result": "successful deletion"})
}
```

# And finally...

Now we're done with the implementation, let's check it

```bash
cd /root/code/api
go mod tidy
go test
```

Output should be

```
PASS
ok      example.com     0.004s
```
