---
services: cognitive-services
manager: nitinme
ms.service: azure-ai-openai
ms.topic: include
author: mrbullwinkle # external contributor: gm2552
ms.author: mbullwin
ms.date: 11/27/2023
---

[Source code](https://github.com/spring-projects-experimental/spring-ai) | [Artifacts (Maven)](https://mvnrepository.com/artifact/org.springframework.ai/spring-ai-core/1.0.0-M1) | [Sample](https://github.com/Azure-Samples/spring-ai-samples/projects/spring-ai-completion)


## Prerequisites

- An Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services?azure-portal=true)
- The current version of the [Java Development Kit (JDK)](https://www.microsoft.com/openjdk)
- The [Spring Boot CLI tool](https://docs.spring.io/spring-boot/docs/current/reference/html/getting-started.html#getting-started.installing.cli)
- An Azure OpenAI Service resource with the `gpt-35-turbo` model deployed. For more information about model deployment, see the [resource deployment guide](../how-to/create-resource.md). This example assumes that your deployment name matches the model name `gpt-35-turbo`

> [!div class="nextstepaction"]
> [I ran into an issue with the prerequisites.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=SPRING&Pillar=AOAI&Product=Chatgpt&Page=quickstart&Section=Prerequisites)

## Set up

[!INCLUDE [get-key-endpoint](get-key-endpoint.md)]

[!INCLUDE [environment-variables](spring-environment-variables.md)]

> [!div class="nextstepaction"]
> [I ran into an issue with the setup.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=SPRING&Pillar=AOAI&Product=Chatgpt&Page=quickstart&Section=Set-up-the-environment)

## Create a new Spring application

Create a new Spring project.

In a Bash window, create a new directory for your app, and navigate to it.

```bash
mkdir spring-ai-completion && cd spring-ai-completion
```

Run the `spring init` command from your working directory. This command creates a standard directory structure for your Spring project including the main Java class source file and the *pom.xml* file used for managing Maven based projects.

```bash
spring init -a spring-ai-completion -n spring-ai-completion --force --build maven -x
```

The generated files and folders resemble the following structure:

```
.
└── spring-ai-completion
    ├── HELP.md
    ├── LICENSE
    ├── LICENSE.md
    ├── README.md
    ├── ai-completion.iml
    ├── mvnw
    ├── mvnw.cmd
    ├── pom.xml
    └── src
        ├── main
        │   ├── java
        │   │   └── com
        │   │       └── example
        │   │           └── ai_completion_demo
        │   │               └── AiCompletionApplication.java
        │   └── resources
        │       └── application.properties
        └── test
            └── java
                └── com
                    └── example
                        └── ai_completion_demo
                            └── AiCompletionApplicationTests.java
```

## Edit the Spring application

1. Edit the *pom.xml* file.

   From the root of the project directory, open the *pom.xml* file in your preferred editor or IDE and overwrite the file with following content:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>3.3.2</version>
            <relativePath/> <!-- lookup parent from repository -->
        </parent>
        <groupId>com.example</groupId>
        <artifactId>ai-completion</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <name>AICompletion</name>
        <description>Demo project for Spring Boot</description>
        <properties>
            <java.version>21</java.version>
        </properties>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter</artifactId>
            </dependency>

            <dependency>
                <groupId>org.springframework.ai</groupId>
                <artifactId>spring-ai-azure-openai-spring-boot-starter</artifactId>
                <version>1.0.0-M1</version>
            </dependency>

            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                </plugin>
                <!-- maven surefire -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <version>${maven-surefire-plugin.version}</version>
                    <configuration>
                        <argLine>-XX:+EnableDynamicAgentLoading</argLine>
                    </configuration>
                </plugin>

            </plugins>
        </build>
        <repositories>
            <repository>
                <id>spring-releases</id>
                <name>Spring Releases</name>
                <url>https://repo.spring.io/milestone/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
            </repository>
        </repositories>
    </project>
```    

1. From the *src/main/java/com/example/ai_completion_demo* folder, open *AiCompletionApplication.java* in your preferred editor or IDE and paste in the following code:

   ```java
   package com.example.ai_completion_demo;

import org.springframework.ai.chat.client.ChatClient;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;


@SpringBootApplication
public class AiCompletionApplication implements CommandLineRunner
{
    private final ChatClient chatClient;

    public AiCompletionApplication(ChatClient.Builder builder) {
        this.chatClient = builder.build();
    }

    public static void main(String[] args) {
        SpringApplication.run(AiCompletionApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {

        String question = "When was Microsoft founded?";
        String answer = null;

        System.out.println(String.format("\r\nThe Question: %s \r\n \r\n", question));

        System.out.println(String.format("Sending completion prompt to AI service. Wait for it..........\r\n"));

        answer = this.chatClient
                .prompt()
                .user(question)
                .call()
                .content();

        System.out.println(String.format("\r\nThe Answer: %s \r\n", answer));

    }

}
```

2. From the *src/main/test/com/example/aicompletiondemo* folder, open *AiCompletionApplicationTests.java* in your preferred editor or IDE and paste in the following code:

```java
package com.example.ai_completion_demo;

import org.junit.jupiter.api.Test;
import org.springframework.ai.chat.client.ChatClient;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.assertTrue;

@SpringBootTest
class AiCompletionApplicationTests {

	@Autowired
	private ChatClient.Builder builder;

	@Test
	void completionTest() {

		ChatClient chatClient = null;
		String question = "When was Microsoft founded?";
		String answer = null;
		String realAnswer = "1975";

		chatClient = builder.build();

		assertTrue(chatClient != null);

		answer = chatClient
				.prompt()
				.user(question)
				.call()
				.content();

		assertTrue(answer.contains(realAnswer));

	}

}
```

   > [!IMPORTANT]
   > For production, use a secure way of storing and accessing your credentials like [Azure Key Vault](/azure/key-vault/general/overview). For more information about credential security, see the Azure AI services [security](../../security-features.md) article.

1. Navigate back to the project root folder, and run the app by using the following command:

   ```bash
   ./mvnw spring-boot:run
   ```

## Output

```output
[INFO] Attaching agents: []

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/

 :: Spring Boot ::                (v3.3.2)

2024-08-16T12:43:59.188-06:00  INFO 97271 --- [AICompletion] [           main] c.e.a.AiCompletionApplication            : Starting AiCompletionApplication using Java 21.0.4 with PID 97271 (/Users/emanley/dev2/azure/microsoft/forkz/spring-ai-samples/projects/spring-ai-completion/target/classes started by emanley in /Users/emanley/dev2/azure/microsoft/forkz/spring-ai-samples/projects/spring-ai-completion)
2024-08-16T12:43:59.190-06:00  INFO 97271 --- [AICompletion] [           main] c.e.a.AiCompletionApplication            : No active profile set, falling back to 1 default profile: "default"
2024-08-16T12:44:00.261-06:00  WARN 97271 --- [AICompletion] [           main] c.a.c.http.netty.implementation.Utility  : The following Netty dependencies have versions that do not match the versions specified in the azure-core-http-netty pom.xml file. This may result in unexpected behavior. If your application runs without issue this message can be ignored, otherwise please update the Netty dependencies to match the versions specified in the pom.xml file. Versions found in runtime: 'io.netty:netty-common' version: 4.1.111.Final (expected: 4.1.101.Final),'io.netty:netty-handler' version: 4.1.111.Final (expected: 4.1.101.Final),'io.netty:netty-handler-proxy' version: 4.1.111.Final (expected: 4.1.101.Final),'io.netty:netty-buffer' version: 4.1.111.Final (expected: 4.1.101.Final),'io.netty:netty-codec' version: 4.1.111.Final (expected: 4.1.101.Final),'io.netty:netty-codec-http' version: 4.1.111.Final (expected: 4.1.101.Final),'io.netty:netty-codec-http2' version: 4.1.111.Final (expected: 4.1.101.Final),'io.netty:netty-transport-native-unix-common' version: 4.1.111.Final (expected: 4.1.101.Final),'io.netty:netty-transport-native-epoll' version: 4.1.111.Final (expected: 4.1.101.Final),'io.netty:netty-transport-native-kqueue' version: 4.1.111.Final (expected: 4.1.101.Final)
2024-08-16T12:44:00.730-06:00  INFO 97271 --- [AICompletion] [           main] c.e.a.AiCompletionApplication            : Started AiCompletionApplication in 1.943 seconds (process running for 2.4)

The Question: When was Microsoft founded?


Sending completion prompt to AI service. Wait for it..........


The Answer: Microsoft was founded on April 4, 1975.  
```

> [!div class="nextstepaction"]
> [I ran into an issue when running the code sample.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=SPRING&Pillar=AOAI&Product=Chatgpt&Page=quickstart&Section=Create-application)

## Clean up resources

If you want to clean up and remove an Azure OpenAI resource, you can delete the resource. Before deleting the resource, you must first delete any deployed models.

- [Azure portal](../../multi-service-resource.md?pivots=azportal#clean-up-resources)
- [Azure CLI](../../multi-service-resource.md?pivots=azcli#clean-up-resources)

## Next steps

For more examples, check out the [Azure OpenAI Samples GitHub repository](https://aka.ms/AOAICodeSamples)
