Task Master Application

This is a Spring Boot application that provides a REST API to create, manage, and run shell commands as "Tasks" within a Kubernetes cluster.

Prerequisites

Java 17+: The project is built with Java 17.

Maven: Used for building the project and managing dependencies.

MongoDB: A MongoDB server running locally (or accessible) on mongodb://localhost:27017.

Kubernetes: A running Kubernetes cluster. Enabling Kubernetes in Docker Desktop is the easiest way to get started locally.

Kubeconfig: Your application needs credentials to talk to Kubernetes. It will automatically pick up your local ~/.kube/config file (just like kubectl does).

How to Run

Start your MongoDB server.

Ensure your Kubernetes cluster is running.

From the project's root directory, run:

mvn spring-boot:run


The application will start on http://localhost:8080.

API Endpoints (curl examples)

Here is how you can test all the required functionality using curl.

1. Create a Task (PUT)

This will create a new task. The ID is 123.

Request:

curl -X PUT http://localhost:8080/tasks \
-H "Content-Type: application/json" \
-d '{
    "id": "123",
    "name": "Print Hello",
    "owner": "John Smith",
    "command": "echo Hello World!"
}'


Response (Example):

{
  "id": "123",
  "name": "Print Hello",
  "owner": "John Smith",
  "command": "echo Hello World!",
  "taskExecutions": []
}


2. Test Command Validation (PUT)

This request should fail with a 400 Bad Request because it contains a ; character.

Request:

curl -v -X PUT http://localhost:8080/tasks \
-H "Content-Type: application/json" \
-d '{
    "id": "456",
    "name": "Bad Task",
    "owner": "Hacker",
    "command": "echo 123 ; rm -rf /"
}'


Response (Example):

< HTTP/1.1 400 Bad Request
...
{"status":400,"error":"Bad Request","message":"Command contains unsafe characters: ;","path":"/tasks"}


3. Get All Tasks (GET)

This returns a list of all tasks in the database.

Request:

curl http://localhost:8080/tasks


Response (Example):

[
  {
    "id": "123",
    "name": "Print Hello",
    "owner": "John Smith",
    "command": "echo Hello World!",
    "taskExecutions": []
  }
]


4. Get Single Task by ID (GET)

This uses the ?id= parameter to fetch the task we created.

Request:

curl "http://localhost:8080/tasks?id=123"


Response (Example):

{
  "id": "123",
  "name": "Print Hello",
  "owner": "John Smith",
  "command": "echo Hello World!",
  "taskExecutions": []
}


5. Find Tasks by Name (GET)

This finds tasks where the name contains the string "Print".

Request:

curl "http://localhost:8080/tasks/find?name=Print"


Response (Example):

[
  {
    "id": "123",
    "name": "Print Hello",
    "owner": "John Smith",
    "command": "echo Hello World!",
    "taskExecutions": []
  }
]


6. Execute a Task (PUT)

This is the main "run" endpoint. It tells the server to execute the command for task 123.

Request:

curl -X PUT http://localhost:8080/tasks/123/execute


Response (Example):
This returns the updated task, now including a taskExecutions entry. Notice the startTime, endTime, and output.

{
  "id": "123",
  "name": "Print Hello",
  "owner": "John Smith",
  "command": "echo Hello World!",
  "taskExecutions": [
    {
      "startTime": "2025-11-12T04:34:10.123Z",
      "endTime": "2025-11-12T04:34:15.456Z",
      "output": "Hello World!\n"
    }
  ]
}


7. Delete a Task (DELETE)

This deletes the task with the given ID.

Request:

curl -v -X DELETE http://localhost:8080/tasks/123


Response (Example):

< HTTP/1.1 204 No Content


8. Verify Deletion (GET)

If you now try to get task 123, you will get a 404 Not Found.

Request:

curl -v "http://localhost:8080/tasks?id=123"


Response (Example):

< HTTP/1.1 404 Not Found
...
Task not found: 123
