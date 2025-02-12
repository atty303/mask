# An Example CLI

> The cli description goes here...





## services

<!-- Cmd description goes here. -->
> Commands related to starting, stopping, and restarting services

### services start (service_name)

> Start or restart a service.

~~~bash
echo "Starting service $service_name"
~~~


### services stop (service_name)

> Stop a service.

~~~bash
echo "Stopping service $service_name"
~~~





## db

> Commands related to the database.

### db list

> List database snapshots.

~~~bash
echo "Listing all database snapshots..."
ls -al
~~~

### db flush

> Commands related to flushing specific databases.

#### db flush postgres

> Flush the postgres database.

~~~bash
echo "Flushed postgres"
~~~

#### db flush redis

> Flush the redis database.

~~~bash
echo "Flushed redis"
~~~

### db snapshot (snapshot_name)

> Take a snapshot of the database.

~~~bash
echo "Saved db snapshot as '$snapshot_name'"
~~~

### db restore (snapshot_name)

> Restore the database to a snapshot.

~~~bash
echo "Restored db to snapshot '$snapshot_name'"
~~~





## serve

> Serve this directory

**OPTIONS**
* port
    * flags: -p --port
    * type: string
    * desc: Which port to serve on

~~~js
const handler = require('serve-handler');
const http = require('http');

const server = http.createServer((request, response) => handler(request, response));

const PORT = Number(process.env.port) || 8080;
server.listen(PORT, () => {
    console.log(`Running at http://localhost:${PORT}`);
});
~~~





## input (arg1) (arg2)

> Example of how to accept user input and sleep

~~~sh
echo "You entered two arguments: $arg1, $arg2"
echo "Enter something:"
read something
echo "You entered: $something"

echo "sleeping..."
sleep 2s
~~~





## format

> Formats files with prettier

**OPTIONS**
* check
    * flags: -c --check
    * desc: Validate files are correctly formatted

~~~sh
alias prettier="./node_modules/.bin/prettier '**/*.{js,jsx,ts,tsx,css,html}'"

if [[ "$check" == "true" ]]; then
    prettier --list-different
else
    prettier --write
fi
~~~





## python (name)

> An example python script

~~~python
import os

name = os.getenv("name", "WORLD")

print("Hello, " + name + "!")
~~~





## ruby (name)

> An example ruby script

~~~ruby
name = ENV["name"] || "WORLD"

puts "Hello, #{name}!"
~~~





## php (name)

> An example php script

~~~php
$name = getenv("name") ?: "WORLD";

echo "Hello, " . $name . "!\n";
~~~
