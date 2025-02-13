# Relatório: Laboratório Java RPC Sem RMI

## Universidade Federal do Ceará  
### Redes de Computadores - Sistemas Distribuídos  
**Francisco Jarbas dos Santos Sousa**  
Quixadá-CE, 2025

---

## Sumário

- [Introdução](#introdu%C3%A7%C3%A3o)
- [Configuração do Projeto](#configura%C3%A7%C3%A3o-do-projeto)
- [Desenvolvimento do Código](#desenvolvimento-do-c%C3%B3digo)
  - [Interface RPC](#interface-rpc)
  - [Implementação do Serviço](#implementa%C3%A7%C3%A3o-do-servi%C3%A7o)
  - [Implementação do Servidor](#implementa%C3%A7%C3%A3o-do-servidor)
  - [Implementação do Cliente](#implementa%C3%A7%C3%A3o-do-cliente)
- [Conclusão](#conclus%C3%A3o)

---

## Introdução

Este laboratório demonstra a implementação de um sistema de **Remote Procedure Call (RPC)** utilizando **Java** e **sockets**, sem o uso do Java RMI (Remote Method Invocation). A abordagem adotada simula chamadas remotas de métodos através de comunicação por sockets, permitindo a interação entre um cliente e um servidor.

---

## Configuração do Projeto

O projeto é estruturado como um projeto **Maven**, com um arquivo `pom.xml` básico contendo as configurações essenciais:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.exemplo</groupId>
    <artifactId>JavaRPC-Sockets</artifactId>
    <version>1.0-SNAPSHOT</version>
</project>
```

---

## Desenvolvimento do Código

A implementação segue a seguinte estrutura:

📂 **src/main/java/com/exemplo**
- `HelloService.java` (Interface RPC)
- `HelloServiceImpl.java` (Implementação do Serviço)
- `Server.java` (Servidor)
- `Client.java` (Cliente)

### Interface RPC
A interface `HelloService` define o contrato do serviço RPC. O método `sayHello(String name)` é declarado para ser chamado remotamente.

```java
package com.exemplo;

// Interface do serviço
public interface HelloService {
    String sayHello(String name);
}
```

### Implementação do Serviço
A classe `HelloServiceImpl` implementa a interface `HelloService`, fornecendo uma lógica simples para responder às requisições.

```java
package com.exemplo;

// Implementação do serviço
public class HelloServiceImpl implements HelloService {
    @Override
    public String sayHello(String name) {
        return "Olá, " + name + "! Este é um exemplo de RPC com sockets.";
    }
}
```

### Implementação do Servidor
O **servidor** escuta conexões na porta `5000`, processa requisições e retorna respostas conforme a chamada remota.

```java
package com.exemplo;

import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
    public static void main(String[] args) {
        try (ServerSocket serverSocket = new ServerSocket(5000)) {
            System.out.println("Servidor pronto na porta 5000...");
            
            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("Cliente conectado!");

                try (BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
                     PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true)) {
                     
                    String input = in.readLine();
                    System.out.println("Requisição recebida: " + input);

                    if (input.startsWith("sayHello:")) {
                        String name = input.split(":")[1];
                        HelloService service = new HelloServiceImpl();
                        String response = service.sayHello(name);
                        out.println(response);
                    } else {
                        out.println("Método não suportado.");
                    }
                }
                clientSocket.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### Implementação do Cliente
O **cliente** estabelece uma conexão com o servidor e faz a chamada RPC enviando uma string formatada.

```java
package com.exemplo;

import java.io.*;
import java.net.Socket;

public class Client {
    public static void main(String[] args) {
        try (Socket socket = new Socket("localhost", 5000);
             BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
             PrintWriter out = new PrintWriter(socket.getOutputStream(), true)) {

            String request = "sayHello:Usuário";
            out.println(request);
            System.out.println("Requisição enviada: " + request);

            String response = in.readLine();
            System.out.println("Resposta do servidor: " + response);

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## Conclusão

O laboratório demonstra como é possível implementar um sistema de **chamadas remotas** usando **sockets** em Java, sem depender de **RMI**. O modelo adotado é simples, mas eficaz para entender conceitos básicos de RPC. No entanto, em sistemas reais, **protocolos mais robustos** como **gRPC** ou **RESTful APIs** são geralmente preferidos para maior escalabilidade e segurança.

---

> **Autor**: Francisco Jarbas dos Santos Sousa  
> **Disciplina**: Sistemas Distribuídos - UFC  
> **Ano**: 2025
