<h1 align="center">Movie App</h1>
<p align="center">
  An app to browse movies
</p>

## Table of contents

1. [Decription](#description)
2. [Technological stack and dependencies](#technological-stack-and-dependencies)
3. [Backend description](#backend-description)
    - [Data model](#data-model)
    - [Architecture](#architecture)
    - [Additional features](#additional-features)
4. [Installation](#installation)
    - [Production mode](#production-mode)
    - [Development mode](#development-mode)

## Description

![Main page](/resources/images/mainPage.png)

This app is a test assignment solution for the Junior Node.js developer position. The app implements the following functionality:

- Adding a movie;
- Deleting a movie;
- Getting movie info;
- Getting a sorted list of movies (by title);
- Getting movies either by actor's name or by title;
- Importing a movie list from a file.

## Technological stack and dependencies

- Web-framework: Express.js;
- DBMS: PostgreSQL;
- ORM: Sequelize;
- Validation: Joi;
- dotenv for reading the environment variables from the .env file;
- Mocha and Sinon for tests;
- ESLint as a linter;
- React.js for building the client-side.

## Backend description

### Data model

The data model is not that complicated in this task. However, there're some ambiguous points:

- Can we consider a movie title and the release year to be a composite primary key for a movie relation? There're a lot of examples of movies with the same title (for instance, consider such titles as Employee Of The Month (2005) (2006)). Though I haven't found the movies with the same title and release year, there's a chance such movies exist (especially when talking about indie movies).
- We also cannot consider actors' names to be the primary key for an actor relation, but for this task, I decided to make a name field unique, so that it is possible to tell when we're referring to the same actor.

So here is the model I've come up with:

![Data Model](/resources/images/dataModel.jpg)

### Architecture

The server-side implements the layered architecture. The layers include:

- **Domain models.** These are Movie, Actor, and MovieActor (as a junction model) models. This layer implements the domain business logic.
- **Service layer.** Includes MovieService, which contains the application business logic implemented by combining the domain logic into certain use-cases and data validation via the validation schemas;
- **Controllers.** Dispatch request handling to services and handle the proper responses.

### Additional features

- Multiple configurations for different purposes via the environment variables;
- Custom logger;
- Graceful shutdown;
- Serving the static with Nginx running inside a Docker container.

## Installation

### Production mode

0. Prerequisites: Node.js, npm, PostgreSQL (with an already created DB), Docker.

1. Clone the repository in the desired folder:

```bash
$ git clone https://github.com/AlexanderKlanovets/movie-app
$ cd movie-app
```

2. Install the dependencies:

```bash
$ npm i
```

3. Run the database migrations:

```bash
$ npx sequelize-cli db:migrate
```

4. Build the client:

```bash
$ cd src/client
$ npm i
$ npm run build
```

5. Run the server:

```bash
$ npm run start
```

**Warning**: Remember to initialize the environment variables.

6. Build a Docker image with Nginx and run it (from the project's root directory). At least these commands are valid for the Ubuntu:

```bash
$ docker build -t 1.0.0 .
$ docker run --add-host host.docker.internal:host-gateway  -p 80:80 -p 443:443 -v `pwd`/src/client/build:/www/ --name nginx-movie-app 1.0.0
```

**Warning**: The application port of the server specified in Nginx configs is 3000. If you need to change it, replace the corresponding value in the file ```vhost.conf``` on the 2nd line.

7. Go to 127.0.0.1 in your browser and you're done.

### Development mode

1. Perform the steps 1-3 from Production mode. To run the server in dev mode execute the following command (it will use nodemon to watch the file system):

```bash
$ npm run start:dev
```

**Warning**: Remember to initialize the environment variables.

2. To run the client in dev mode (you need to go to the client's folder, use the first command below if you are in the project's root folder):

```bash
$ cd src/client
$ npm i
$ npm start
```

**Warning**: Please make sure that the proxy value inside the client's package.json corresponds to the actual server.

3. Go to 127.0.0.1:3000 in your browser and you're done.