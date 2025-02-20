# Key-Cloak-2025-v26.0.6

The line-by-line tutorial for you to simply understand how to run a container with Keycloak

![image](https://github.com/user-attachments/assets/145b34a5-a225-438f-a458-37c0d7e38352)



## **Introduction**
OpenCTI is an open source platform allowing organizations to manage their cyber threat intelligence knowledge and observables. It has been created in order to structure, store, organize and visualize technical and non-technical information about cyber threats.

The structuration of the data is performed using a knowledge schema based on the STIX2 standards. It has been designed as a modern web application including a GraphQL API and an UX oriented frontend. Also, OpenCTI can be integrated with other tools and applications such as MISP, TheHive, MITRE ATT&CK, etc.

## **Installation**

To install Project Title, follow these steps:

**Prerequisites to run**
To run docker-compose, you need to have docker installed. If you don’t have it, you can find how to install it [here](https://docs.docker.com/get-docker/).

**Creating the file**
Docker Compose is a tool that helps define and manage multi-container Docker applications. It uses a YAML file to configure the application’s services, networks, and volumes. Let's create the docker-compose.yml file and define the Keycloak service:

_services:
  my-keycloak:_

Now we need to tell Docker what Keycloak image use. I will use the quay.io official Keycloak image. At the moment (21/02/2025), the Keycloak 26.1.6 is the last version.

_services:
  my-keycloak:
    image: quay.io/keycloak/keycloak:26.1.6_

Keycloak has some mandatory variables that we need to define for it to work, they are defined inside the "environment" token:
```
_services:
  my-keycloak:
    image: quay.io/keycloak/keycloak:24.0
    environment:
      KC_HOSTNAME: localhost
      KC_HOSTNAME_PORT: 7080
      KC_HOSTNAME_STRICT_BACKCHANNEL: "true"
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: admin
      KC_HEALTH_ENABLED: "true"
      KC_LOG_LEVEL: info_
```
In summary, the variables and their function:

* _KC_HOSTNAME_: Hostname used by Keycloak. Running locally must be "localhost".
* _KC_HOSTNAME_PORT_: Keycloak container internal port. Default is 7080.
* _KC_HOSTNAME_STRICT_BACKCHANEL_: Enables or disables backchannel communication between servers. Personally, I leave it enabled to use propagated logout and token sharing.
* _KEYCLOAK_ADMIN_: Keycloak admin user username.
* _KEYCLOAK_ADMIN_PASSWORD_: Keycloak admin user password.
* _KC_HEALTH_ENABLED_: Enables Keycloak health endpoints, enabling server health status checks. This is useful for monitoring and ensuring that Keycloak is working correctly.
* _KC_LOG_LEVEL_: Set the log level for Keycloak. You can check the possible log levels here.

As I enabled _KC_HEALTH_ENABLED_, we need to define how the healthcheck will be performed:
```
_services:
  my-keycloak:
    image: quay.io/keycloak/keycloak:24.0
    environment:
      KC_HOSTNAME: localhost
      KC_HOSTNAME_PORT: 7080
      KC_HOSTNAME_STRICT_BACKCHANNEL: "true"
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: admin
      KC_HEALTH_ENABLED: "true"
      KC_LOG_LEVEL: info
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:7080/health/ready"]
      interval: 15s
      timeout: 2s
      retries: 15_
```
Basically an http request will be sent to _http://localhost:7080/health/ready_ every 15 seconds to check if everything is ok. 
If it gives a timeout of 2 seconds, it will try 15 times, and if it still doesn’t give a health signal, Docker will mark the container as “unhealty” and take the necessary action.

Now let’s define the command to run Keycloak:
```
_services:
  my-keycloak:
    image: quay.io/keycloak/keycloak:24.0
    environment:
      KC_HOSTNAME: localhost
      KC_HOSTNAME_PORT: 7080
      KC_HOSTNAME_STRICT_BACKCHANNEL: "true"
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: admin
      KC_HEALTH_ENABLED: "true"
      KC_LOG_LEVEL: info
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:7080/health/ready"]
      interval: 15s
      timeout: 2s
      retries: 15
    command: ["start-dev", "--http-port", "7080", "--https-port", "7443"]_
```
Finally, we will expose the container ports to be accessible from the outside and, as a best practice, create a network in Docker for communication between containers, if necessary:
```
_services:
  my-keycloak:
    image: quay.io/keycloak/keycloak:24.0
    environment:
      KC_HOSTNAME: localhost
      KC_HOSTNAME_PORT: 7080
      KC_HOSTNAME_STRICT_BACKCHANNEL: "true"
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: admin
      KC_HEALTH_ENABLED: "true"
      KC_LOG_LEVEL: info
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:7080/health/ready"]
      interval: 15s
      timeout: 2s
      retries: 15
    command: ["start-dev", "--http-port", "7080", "--https-port", "7443"]
    ports:
      - "7080:7080"
      - "7443:7443"
    networks:
      - local_network
networks:
  local_network:
    driver: bridge_
```
To run this container, simply enter the root folder that contains the _docker-compose.yml_ file and run the following command:
```
_docker-compose up_
```
## **Usage**

To use Project Title, follow these steps:

1. Access KeyCloak using a Web Browser on port 7443 (as set in the docker file)
2. Optional: Ensure to enable MFA inside KeyCloak
3. Optional: Ensure to use Reverse Proxy to present Custom SSL certificate for authetntic, signed and verified HTTPS access

## **Contributing** (Share is caring)

If you'd like to contribute to Project Title, here are some guidelines:

1. Fork the repository.
2. Create a new branch for your changes.
3. Make your changes.
4. Write tests to cover your changes.
5. Run the tests to ensure they pass.
6. Commit your changes.
7. Push your changes to your forked repository.
8. Submit a pull request.

## **License**

Project Title is released under the MIT License. See the **[LICENSE](https://www.blackbox.ai/share/LICENSE)** file for details.

## **Authors and Acknowledgment**

Project Title was created by **[Your Name](https://github.com/username)**.

Additional contributors include:

- **[Contributor Name](https://github.com/contributor-name)**
- **[Another Contributor](https://github.com/another-contributor)**

Thank you to all the contributors for their hard work and dedication to the project.

## **Code of Conduct**

Please note that this project is released with a Contributor Code of Conduct. By participating in this project, you agree to abide by its terms. See the **[CODE_OF_CONDUCT.md](https://www.blackbox.ai/share/CODE_OF_CONDUCT.md)** file for more information.

## **FAQ**

**Q:** What is Project Title?

**A:** Project Title is a project that does something useful.

**Q:** How do I install Project Title?

**A:** Follow the installation steps in the README file.

**Q:** How do I use Project Title?

**A:** Follow the usage steps in the README file.

**Q:** How do I contribute to Project Title?

**A:** Follow the contributing guidelines in the README file.

**Q:** What license is Project Title released under?

**A:** Project Title is released under the MIT License. See the **[LICENSE](https://www.blackbox.ai/share/LICENSE)** file for details.

## **Changelog**

- **0.1.0:** Initial release
- **0.1.1:** Fixed a bug in the build process
- **0.2.0:** Added a new feature
- **0.2.1:** Fixed a bug in the new feature

## **Contact**

If you have any questions or comments about Project Title, please contact **[Your Name](you@example.com)**.

## **Conclusion**

That's it! This is a basic template for a proper README file for a general project. You can customize it to fit your needs, but make sure to include all the necessary information. A good README file can help users understand and use your project, and it can also help attract contributors.
