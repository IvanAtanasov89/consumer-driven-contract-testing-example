# Java Pact Example

Simple example project that uses pacts with Java.

There are 2 sub projects:
1. provider - A rest service with a hello world endpoint.
2. consumer - A consumer of the hellow world endpoint

With this project you will be able to
1. Create a pact using the consumer
2. Publish it to a pact broker
3. Verify that the provider fullfills the pact


## Steps

### Start a pact broker

In the pact-broker folder there is a docker compose file. 
This manages starting up a pact broker and a postgres DB that it requires

```sh
cd pact-broker
docker-compose up
```

Try navigating to localhost:80 in a browser and you should see the pact broker UI but no pacts... yet.

### Publish a pact

Consumers are always responsible for creating pacts.

A json file describing the pact can be generated with out example consumer by simply running the test.
This occurs because the tests are annotated with annotations provided by the pact JVM plugin.

The test code is actually starting a mock endpoint for the test and recording the interactions.

To create a pact in the consumer/build/pacts simply run:

```sh
./gradlew :consumer:test
```

You can publish the pact using (Note that the test intermediate step is not necessary as pactPublish depends on test):

```sh
./gradlew :consumer:pactPublish
```

If all goes well you should now see a pact present in the pact broker UI.

### Start the provider service

Ideally this would be handled as part of the verify task itself and I'll probably make this happen at some point.
However for now you will need to start up the service. Do this in a new shell tab:

```sh
./gradlew :provider:build
java -jar provider/build/libs/gs-actuator-service-0.1.0.jar
```

### Verify the pact

Now you can verify the pact against the service you have just started

Just do this:

```sh
./gradlew :provider:pactVerify
```

You should see some output saying the pact was verified.

## Other points

I explicitly tag the pact with INTEGRATION. This is just experimenting.

When fetching pacts to verify it gets the latest version with this tag. In the current setup it also verifies the latest
version regardless of tag.

Don't seem to be able to verify against versions.

Thanks to 'The Creative Tester' as this is heavily based on their work:
- http://the-creative-tester.github.io/Java-Consumer-Driven-Contract-Testing/
- https://github.com/the-creative-tester/consumer-driven-contract-testing-example