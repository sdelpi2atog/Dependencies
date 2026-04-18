# Aviso

Arquivos produzidos durante um curso aberto. O material original não é meu e não deve ser redistribuído.

# Geral

## Links de partida

A aula sobre segurança em APIs REST está na plataforma do curso, e o código base fica num repositório público da trilha.


## Habilitando

![segurança habilitada](images/enabling-spring-secutity.png)


## Projeto gerado

Foi assim que gerei o projeto no gerador oficial:

![projeto gerado](images/project-generated.png)

Baixei o pacote gerado e descompactei na raiz.

Também precisei criar o arquivo de propriedades da aplicação com a porta definida:

```
server.port=8081
```

Depois disso, os comandos de sempre:

```
mvn install
mvn spring-boot:run
```

Abrindo o endereço local, a página inicial apareceu:

![página inicial](images/initial-page.png)

Para entrar com o usuário padrão, a senha é impressa no console na primeira execução:

![senha padrão](images/default-password.png)

Já um usuário fixo (não recomendado, só para simplificar o teste) vai direto no arquivo de propriedades:

```
spring.security.user.name=user
spring.security.user.password=password
spring.security.user.roles=USERS
```


## Autenticação simples em memória

A aula mostrou a configuração em memória dessa forma. Boa parte dela já está marcada como obsoleta na versão atual, então registrei só num vídeo curto, sem manter o código aqui.


## Opções de criptografia

![opções de criptografia](images/cryptography-options.png)


## Controle de acesso por perfil

O controlador ficou assim:

```
package dio.spring.security.controllers;

import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class TelaInicialController {
    @GetMapping
    @PreAuthorize("hasAnyRole('managers', 'users')")
    public String inicio(){
        return "Bem-vindo à API";
    }

    @PreAuthorize("hasAnyRole('users')")
    @GetMapping("/users")
    public String usuarios() {
        return "Acesso de usuário comum";
    }

    @PreAuthorize("hasAnyRole('managers')")
    @GetMapping("/managers")
    public String gestores() {
        return "Acesso de gestor";
    }
}
```

E o arquivo de propriedades por trás dele:

```
server.port=8081
spring.security.user.name=user
spring.security.user.password=password
spring.security.user.roles=users
```

Só esses dois arquivos foram alterados até aqui. Com o usuário criado:

- as rotas abaixo respondem normalmente:
  - /
  - /users
- e esta devolve erro de acesso negado:
  - /managers

Há um vídeo mostrando exatamente esse comportamento.


## A classe de configuração

A aula apresentou a classe de configuração que herda do adaptador de segurança, mas ela está marcada como obsoleta na versão atual — não é culpa do professor, a biblioteca mudou. Por isso não consegui importar a classe usada na herança para completar o teste, e não cheguei a testar a alternativa sugerida nos fóruns.

Resumindo: o professor moveu o controle de acesso por perfil do controlador para uma classe de configuração separada. Particularmente prefiro manter no controlador — é uma anotação a mais e evita abrir outro arquivo.