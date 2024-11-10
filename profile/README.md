# Polyglot Experiment
<!--
https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax
-->
This experiment is a integral component of academic research that seeks to obtain insights on the use of a polyglot persistence API created on [Esfinge Query Builder](https://github.com/EsfingeFramework/querybuilder/blob/master/documentation/README.md).

## Prerequisites
- Proficiency in programming with Java 11 or higher and familiarity with annotations.
- A knowledge of DAO and ORM design patterns is helpful.

## Settings
The experiment consists of Java projects for which the volunteer must develop what is requested in each one. In this section, it is important to understand what needs to be defined in your development environment.
The Java projects in this experiment are projects configured with Maven. This drastically reduces any incompatibility that may occur. If you already have a Java environment set up, no changes will probably be necessary.

### JDK
The Java projects in this experiment require you to have at least JDK 11 or higher installed. You can download the version specific to your operating system here [https://adoptium.net/temurin/releases/](https://adoptium.net/temurin/releases/)

### IDE
The entire experiment was tested using the IDEs [Netbeans](https://netbeans.apache.org/front/main/index.html) and [Intellij IDEA](https://www.jetbrains.com/pt-br/idea/) on the Windows 10 and 11 and Linux Ubuntu 24.04.1 LTS operating systems. Don't worry if your IDE or operating system is different. You will hardly have any problems running the projects in any IDE that supports Java development. Some IDEs that can be used:

- [Netbeans](https://netbeans.apache.org/front/main/index.html) 
- [Intellij IDEA](https://www.jetbrains.com/pt-br/idea/)
- [Eclipse](https://eclipseide.org/)

### Firewall
The Java projects in this experiment access the remote server mongodb.cemaden.gov.br externally on ports 5432 and 27017. If prompted, allow your IDE or Java process to also access this.

## Background
Neste experimento serão propostas tarefas em projetos Java que fazem uso do framework Esfinge Query Builder. No entanto não é necessário experiência prévia ou conhecimentos muito específicos sobre este framework. Caso deseje você pode aprender sobre este framework [clicando aqui](https://github.com/EsfingeFramework/querybuilder/blob/master/documentation/README.md). O plano de fundo deste exeperimento está associado aos desafios da persistência poliglota em um mesmo domínio de aplicação. Desta forma para facilitar a compreensão geral sobre todo o background necessário, será exposto aqui um exemplo completo de aplicação. A ideia é mostrar tudo que é necessário você compreender para executar as tarefas que serão propostas posteriormente.

Imagine que o domínio de aplicação seja de Gestão de Marketing de Site De Vendas. Suponha que você tem usuários registrados em uma base de dados relacional PostgreSQL e registros de visitas a páginas de produtos registrados em uma base NoSQL MongoDB. Considere que o usuário deu concentimento para registro de suas atividades no site. Perceba que os dados das duas bases distintas pertencem ao mesmo domínio. A ideia é conseguir correlacionar estes dados de forma a obter informações sobre, por exemplo, **Qual página é mais visualizada por usuários com mais de 30 anos?**, este conhecimento é útil para anúncios relevantes e direcionados.

Consideremos que a tabela User está mapeada utilizando [JPA (Java Persistence API)](https://www.oracle.com/java/technologies/persistence-jsp.html), conforme a seguir.
``` Java
@Entity
@PersistenceType(value = JPA1)
public class User {
    @Id
    @GeneratedValue
    private long id;
    private String name;
    private String lastName;
    private Strig login;
    private String password;
    private int age;
    // getters and setters
}
```

Consideremos também que a coleção Visit está mapeada utilizando [Morphia ODM](https://www.mongodb.com/resources/languages/morphia), conforme a seguir.
``` Java
@Entity
@PersistenceType(value = MongoDB)
public class Visit {
    @Id
    private ObjectId id;
    private Stringr client;
    private String page;
    private LocalDateTime datehour;
    // getters and setters
}
```

Vamos inicialmente utilizar o Esfinge Query Builder para prover acesso a estas bases a partir da criação de DAOs. Esfinge Query Builder offers a solution for creating a persistence layer simply and quickly. Using the philosophy "for a good framework, the method name is enough", Query Builder uses the method names of an interface to infer the queries that need to be executed on the database. The first step is to define an interface with the method names for the queries that need to be made. Optionally, when you want CRUD operations to be available, you can extend the Repository interface.

``` Java
public interface UserDAO extends Repository<User> {
    List<User> getUserByName(String name);
    List<User> getUserByLastName(String lastname);
    List<User> getUserByLogin(String login);
    List<User> getUserByAgeGreater(Integer age);
}
```

``` Java
public interface VisitDAO extends Repository<Visit> {
    List<Visit> getVisitByClient(String client);
    List<Visit> getVisitByPage(String page);
    List<Visit> getVisitByDatehour(LocalDateTime dateHour);
}
```

The second step is to create the interface and use it. Is that all? That is it! The idea of the Query Builder is to really make creating the persistence interface very simple. Here is how to create the instance of the class that actually implements this interface:

``` Java
UserDAO userDAO = QueryBuilder.create(UserDAO.class);
VisitDAO visitDAO = QueryBuilder.create(VisitDAO.class);
```

> [!NOTE]
> _Esfinge Query Builder é um framework extensível e pode ser aplicado a diversos tipos de banco de dados. Atualmente possui extensão para JDBC, JPA, MongoDB, Cassandra e Neo4J. Desta forma pode lidar bem com o exemplo fornecendo uma API unificada para acesso transparente a diversos tipos de bancos_.

Considerando tudo acima, voltemos a pergunta: **Qual página é mais visualizada por usuários com mais de 30 anos?**

Para responder esta pergunta vamos levar em consideração um ponto em comum que servirá como chave entre as bases. Sob a perspectiva da metodologia DDD, definimos User como a entidade primária e Visit como um ValueObject. De forma simplificada, no exemplo aqui demostrando o campo client de Visit possui o valor do campo login em User. Desta forma podemos estabelecer a correlação usando estes campos.

1) A forma **simples** de responder esta questão é criando um método na classe de controller e utilizandos os dois DAOs de forma independente conforme abaixo:

``` Java

private static UserDAO userDAO;
private static VisitDAO visitDAO;

public MyController(){
    UserDAO dao = QueryBuilder.create(UserDAO.class);
    VisitDAO dao = QueryBuilder.create(VisitDAO.class);
}

public String getMostViewedPageByGreaterAge(int age) {

    Map<String, Integer> mostViewedPages = new HashMap();

    // usar userDAO para recuperar os usuários com mais de 30 anos
    List<User> users = userDAO.getUserByGreaterAge(30);

    // iterar os usuários e para cada um recuperar user.getLogin()
        String loginValue = user.getLogin();

        // usar visitDAO para recuperar as visitas com login igual a loginValue
        List<Visit> visits = visitDAO.getVisitByClient(loginValue);

        // iterar as visitas do usuário e para cada uma recuperar visit.getPage()
             String page = visit.getPage();

            // adicionar page em mostViewedPages atualizando o valor de quantas vezes aparece
            // omitted code

    // retornar o page com maior valor
    // omitted code
}
```

2) Uma forma mais elaborada de responder esta questão sob a perspectiva de uma API de persistência poliglota é considerar as duas entidades como correlacionadas no mesmo domínio usando apenas o DAO da entidade primária para recuperar os dados. Para tanto, precisamos realizar algumas mudanças em nossa classe User e no nosso método getMostViewedPageByGreaterAge da classe de controller.

``` Java

private static UserDAO userDAO;

public MyController(){
    UserDAO dao = QueryBuilder.create(UserDAO.class);
}

public String getMostViewedPageByGreaterAge(int age) {

    Map<String, Integer> mostViewedPages = new HashMap();

    // usar userDAO para recuperar os usuários com mais de 30 anos
    List<User> users = userDAO.getUserByGreaterAge(30);

    // iterar os usuários e para cada um recuperar user.getVisits()

        // iterar as visitas do usuário e para cada uma recuperar visit.getPage()
             String page = visit.getPage();

            // adicionar page em mostViewedPages atualizando o valor de quantas vezes aparece
            // omitted code

    // retornar o page com maior valor
    // omitted code
}
```

Perceba que neste cenário, a partir de uma API unificada foi possível acessar dados de um mesmo domínio mesmo em bases distintas de forma verdadeiramente transparente para o desenvolvedor.




## Procedure <sup>(for the supervisor)</sup>
Four tasks are provided: **Simple1A**, **Polyglot2A**, **Polyglot1B***, and **Simple2B**. It involves domains: 1 and 2, with the application of two techniques: simple and polyglot, solved by two groups: A and B.

This experiment employs a crossover methodology. To validate the methodology, the experiment must be executed as outlined below:

- Division of the volunteer cohort into two groups: **A** and **B**;
- Group **A** will perform the tasks **Simple1A** and **Polyglot2A** in that order;
- Group **B** will perform the tasks **Polyglot1B** and **Simple2B** in that order.

> [!IMPORTANT]
> The division into groups and the sequence of task execution is essential.
> 
> Additionally, please take into account a time interval between the execution of tasks, if feasible.

After completing all tasks, the volunteer must respond to the questionnaire. 

## Experiment Start 💻:

To access the description of each task, click below:

**A Group**
- [**Simple1A**](https://github.com/PolyglotExperiment/Simple1A)
- [**Polyglot2A**](https://github.com/PolyglotExperiment/Polyglot2A)

**B Group**
- [**Polyglot1B**](https://github.com/PolyglotExperiment/Polyglot1B)
- [**Simple2B**](https://github.com/PolyglotExperiment/Simple2B)

## Experiment Finish ✔️:
After completing the tasks, the volunteer must respond to the questionnaire available at the link. 




