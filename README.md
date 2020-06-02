# Maven Dependencies Proxy for Docker Multistage Builds
Local maven cache setup

This approach is 3-4 times faster then anything i've tried e.g RUN mvn dependency:go-offline

The most portable solution till now seems to be a local nexus image running locally proxying the dependency downloads.

The problem with a prepackaged image that already has the dependencies is that you will have to first rebuild the image builder before you run your multistage maven build for the project.

Pull the image

```$ docker pull sonatype/nexus3```

Create a persistent volume:

```$ docker volume create --name nexus-data```

Run the image
```$ docker run -d -p 8081:8081 --name nexus -v nexus-data:/nexus-data sonatype/nexus3```

Get in the container console:
```docker exec -it <container-id> bash```

```cat /nexus-data/admin.password```

Create a Repository Group

add a settings file that mirrors all repos and points them to the Repository Group

in your Dockerfile copy the [settings file](settings.xml)

```COPY settings.xml "/root/.m2/settings.xml"```
...

[Nexus 3 step by step proxy](https://help.sonatype.com/learning/repository-manager-3/first-time-installation-and-setup/lesson-2%3A-proxy-and-hosted-maven-repositories)

here you can finde an example for a springboot application that uses a skaffold build for the local Kubernetes Cluster.
[github project](https://github.com/aballaci/springboot-jpa-sakila)

