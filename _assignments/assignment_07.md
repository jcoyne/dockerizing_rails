# Assignment 7 - Glueing things together

## Preparing the Compose file
Now that we have a clean environment, let's put the `docker-compose.yml` in place:
```
cp _examples/docker-compose.yml .
```

Take some time to get familiar with the content of the file.

## Starting the services
Open the file and make the changes in the appropriate places. This should just be the name of the image that will be used. Once you are done, go ahead and start the environment with:
```
docker-compose up -d
```

The `-d` flag will start all services in the background. This allows us to keep working in our current shell.

We can make sure that all services are running with
```
docker-compose ps
```
You should see two containers. The `State` column should say `Up`.

In order to see what our containers tell us on `STDOUT` we can use the following command:
```
docker-compose logs
```

We can also append some additional flags to the `docker-compose logs` command. Use the `--help` option to find out more. Getting to know the `docker-compose logs` command will be __very help for debugging any issues__ that come up.

## Getting the app up and running
Try browsing http://localhost:3000 - you will see that the database has not been migrated. That makes sense, we just created a whole new set of containers and volumes.

> **Note**: Docker Compose automatically prefixes all resources with the project name (the name of the current directory per default).

So let's run the migrations. Just as with the Docker CLI, we can run arbitrary commands in the context of a service:
```
docker-compose run --rm app rake db:create db:migrate db:test:prepare
```

Now the web application should work as expected. The `--rm` in the command deletes the container right after it terminates. This is useful because we don't have to clean up after ourselves. Go ahead and browse http://localhost:3000 to verify it works as expected.

Let's try some other things - this time we will run the test suite:
```
docker-compose run --rm app rspec
```

And we can easily start a rails console as well:
```
docker-compose run --rm app rails c
```

> **Note**: We don't have to specify the `-it` flags with `docker-compose run`. Docker Compose takes care of that for us automatically when using the `run` command.


### Shutting things down
You can stop everything by running
```
docker-compose down
```

This will delete the container, but keep the volumes intact. Use the `-v` flag if you also want to delete the volumes defined in the `docker-compose.yml`.

## Building images
Docker Compose also enables us to build our images with a single command:
```
docker-compose build
```

Docker Compose will build the image for the service `app` because we specified the `build` directive. It automatically tags the image with the name specified by the `image` directive.

We can combine building the image with the `up` command as well:
```
docker-compose up -d --build
```

### Bonus
* What will happen if you just run the following command? Any idea why?
  ```
  docker-compose run --rm app
  ```
* Import seed data by running `rake db:seed`
* What happens when you shut down the services with `docker-compose down` and then run the test suite?


# What changed
You can find our changes in the [`glueing_things_together`](https://github.com/jcoyne/dockerizing_rails/tree/glueing_things_together) branch. [Compare it](https://github.com/jcoyne/dockerizing_rails/compare/utilizing_layers...glueing_things_together) to the previous branch to see what changed.

[Go to next assignment](assignment_08.md) |
[Go to previous assignment](assignment_06.md) |
[Back to the overview](../README.md#assignments)
