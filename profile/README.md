# Polyglot Experiment
<!--
https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax
-->

𝗙𝗥𝗘𝗘 𝗔𝗡𝗗 𝗖𝗟𝗔𝗥𝗜𝗙𝗜𝗘𝗗 𝗖𝗢𝗡𝗦𝗘𝗡𝗧 𝗧𝗘𝗥𝗠 (𝗙𝗖𝗖𝗧)

You are being invited to participate in the research entitled  "A transparent polyglot persistence layer approach to accessing different types of databases in the same domain model _(provisional name)_".

𝗣𝗮𝗿𝘁𝗶𝗰𝗶𝗽𝗮𝘁𝗶𝗼𝗻: The experiment is divided in two parts. In the first you will be asked to accomplish two implementation tasks with [Esfinge Query Builder](https://github.com/EsfingeFramework/querybuilder/blob/master/documentation/README.md), one using the Conventional Approach and other using the Polyglot Approach.
The instructions for this part of the experiment can be found here in next sections.

In the second part, you will be requested to complete a questionnaire, which may be found at [https://forms.gle/QwuAV6opgTsqCDK69](https://forms.gle/QwuAV6opgTsqCDK69).

𝗖𝗼𝗻𝗳𝗶𝗱𝗲𝗻𝘁𝗶𝗮𝗹𝗶𝘁𝘆 𝗮𝗻𝗱 𝗮𝗻𝗼𝗻𝘆𝗺𝗶𝘁𝘆: The information in this research is strictly confidential, and disclosed only in scientific events or publications and will only be used for this purpose, with no identification of the participant(s), unless between those responsible for the study, ensuring full confidentiality about their participation.

𝗖𝗼𝗻𝘁𝗮𝗰𝘁 𝗶𝗻𝗳𝗼𝗿𝗺𝗮𝘁𝗶𝗼𝗻 𝗳𝗼𝗿 𝘁𝗵𝗼𝘀𝗲 𝗿𝗲𝘀𝗽𝗼𝗻𝘀𝗶𝗯𝗹𝗲 𝗳𝗼𝗿 𝘁𝗵𝗲 𝗿𝗲𝘀𝗲𝗮𝗿𝗰𝗵: During the research period, you have the right to ask any questions or ask for any other clarification, simply contacting one of the researchers by the e-mail address fernando.opc@gmail.com.
 
𝗩𝗼𝗹𝘂𝗻𝘁𝗮𝗿𝘆 𝗣𝗮𝗿𝘁𝗶𝗰𝗶𝗽𝗮𝘁𝗶𝗼𝗻: You have the right to refuse to participate in the referred experiment or to withdraw from this study, at any time, without prejudice or retaliation, for your voluntary decision. This experiment can be used for educational purposes as a development activity. In this scenario, sharing your data in support of research is also voluntary.

𝗗𝗮𝘁𝗮 𝗿𝗲𝘁𝗲𝗻𝘁𝗶𝗼𝗻: The data will be kept after the completion of the project for 5 years.

𝗖𝗼𝗻𝘀𝗲𝗻𝘁 𝗥𝗲𝗴𝗶𝘀𝘁𝗿𝗮𝘁𝗶𝗼𝗻: Once the questionnaire is being carried out using an electronic form, you must check the option in which you affirm that you agree with the participation of the study and make a copy of it after completing it. You can also request a copy of the document signed by the researchers.

## A) Prerequisites 📋
- Proficiency in programming with Java 11 or higher and familiarity with annotations.
- Knowledge with DAO and ORM design patterns is helpful.

## B) Settings 🛠️
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
The Java projects in this experiment access the remote servers _mongodb.cemaden.gov.br_ externally on ports _5432_ and _27017_. If prompted, allow your IDE or Java process to also access this.

## C) Background 📖
In this experiment, tasks will be proposed in Java projects that make use of the [Esfinge Query Builder](https://github.com/EsfingeFramework/querybuilder/blob/master/documentation/README.md) framework. However, prior experience or very specific knowledge about this framework is not required. If you wish, you can learn about this framework [by clicking here](https://github.com/EsfingeFramework/querybuilder/blob/master/documentation/README.md). The background of this experiment is associated with the challenges of polyglot persistence in the same application domain. In this way, to facilitate the general understanding of all the necessary background, a complete application example will be presented here. The idea is to show everything you need to understand to perform the tasks that will be proposed later.

### C1) Exemplification
Imagine that the application domain is E-commerce Marketing Management. Suppose you have users registered in a PostgreSQL relational database and records of visits to product pages stored in a MongoDB NoSQL database. Consider that the user has given consent for the recording of their activities on the site. Notice that the data from the two distinct databases belong to the same domain. The idea is to be able to correlate this data in order to obtain information about, for example, **How many users above the age of 21 visited the my.example.shop/games page?**, this knowledge is useful for relevant and targeted advertisements.

Let's consider that the **User** table is mapped using [JPA (Java Persistence API)](https://www.oracle.com/java/technologies/persistence-jsp.html), as follows.

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

Let's also consider that the Visit collection is mapped using [Morphia ODM](https://www.mongodb.com/resources/languages/morphia), as follows.

``` Java
@Entity
@PersistenceType(value = MongoDB)
public class Visit {
    @Id
    private ObjectId id;
    private String client;
    private String page;
    private LocalDateTime datehour;
    // getters and setters
}
```

We will initially use the Esfinge Query Builder to provide access to these databases. Esfinge Query Builder offers a solution for creating a persistence layer simply and quickly. Using the philosophy "for a good framework, the method name is enough", Query Builder uses the method names of an interface to infer the queries that need to be executed on the database. The first step is to define an interface with the method names for the queries that need to be made. Optionally, when you want CRUD operations to be available, you can extend the Repository interface.

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
> Esfinge Query Builder is an extensible framework and can be applied to several types of databases. Currently, it has extensions for JDBC, JPA, MongoDB, Cassandra, and Neo4J.

Given all of this, let us return to the question: **How many users above the age of 21 visited the my.example.shop/games page?**

To address this question, we'll look at a common point that will act as a key between the bases. In line to the DDD methodology (Domain Driven Design), the primary entity is **User**, whereas **Visit** is a value object. In the example provided, the **Visit** **client field** has the value from the **User** **login field**. Using these fields, we can establish a correlation.

#### C1.1) Conventional Approach
The **conventional approach** to addressing the highlighted issue is to create a method in the controller class and use the two DAOs independently as demonstrated below <sup>_(attention to the code comments)_</sup>:

``` Java
private static UserDAO userDAO;
private static VisitDAO visitDAO;

public MyController() {
    userDAO = QueryBuilder.create(UserDAO.class);
    visitDAO = QueryBuilder.create(VisitDAO.class);
}

public int countVisitsToPageByGreaterAge(String page, int age) {
    int result = 0;
    // use userDAO to retrieve users over "age paramater" years old
    List<User> users = userDAO.getUserByGreaterAge(age);
    // iterate users
    for (User user : users) {
        // use visitDAO to retrieve visits with "client attribute" equal to "user login"
        List<Visit> visits = visitDAO.getVisitByClient(user.getLogin());
        // iterate visits 
        for (Visit visit : visits) {
            //checks if the visited page is equal to the "page parameter".
            if (visit.getPage().equals(page)) {
                result++;
            }
        }
    }
    return result;
}
```

#### C1.2) Polyglot Approach
The **polyglot approach** to solve this question from the perspective of a polyglot persistence API is retrieving data using the principal entity's. To do this, we must modify our **User** class as well as the controller class's **countVisitsToPageByGreaterAge** method.

In the User class, we will utilize annotations to notify the Esfinge Query Builder framework that we are employing polyglot features.

1. We changed the annotation from **@PersistenceType(value = JPA1)** to **@PersistenceType(value = JPA1, secondary = MONGODB)**. This informs the framework that a different database type, specifically MongoDB, is associated with one of the class attributes.
2. We create the **visits** attribute and mark it with JPA's **@Transient** to indicate it is not linked to the primary persistence type.
3. The **visits** attribute is annotated with the **@PolyglotOneToMany(referencedEntity = User.class)** annotation, indicating the primary entity as a reference.
4. We use the **@PolyglotJoin(name = "client", referencedAttributeName = "login")** annotation on the **visits** attribute to specify **client**  attribute from the **Visit** class as the _name_, and the **login** (class attribute from class referenced in **@PolyglotOneToMany**)  as _referencedAttributeName_.
5. Done!

See the full class below<sup>_(attention to the code comments)_</sup>:

``` Java
@Entity
@PersistenceType(value = JPA1, secondary = MONGODB)
public class User {
    @Id
    @GeneratedValue
    private long id;
    private String name;
    private String lastName;
    private Strig login;
    private String password;
    private int age;

    @Transient
    @PolyglotOneToMany(referencedEntity = User.class)
    @PolyglotJoin(name = "client", referencedAttributeName = "login")
    private List<Visit> visits;

    // getters and setters
}
```

``` Java
private static UserDAO userDAO;

public MyController() {
    userDAO = QueryBuilder.create(UserDAO.class);
}

public int countVisitsToPageByGreaterAge(String page, int age) {
    int result = 0;
    // use userDAO to retrieve users over "age paramater" years old
    List<User> users = userDAO.getUserByGreaterAge(age);
    // iterate users
    for (User user : users) {
        // iterate visits 
        for (Visit visit : user.getVisits()) {
            //checks if the visited page is equal to the "page parameter".
            if (visit.getPage().equals(page)) {
                result++;
            }
        }
    }
    return result;
}
```

### C2) Exemplification review

In subsection C1) you were introduced to everything necessary to complete the tasks described in the experiment.

**We believe that the most effective documentation for elucidating a coding approach comprises a concise description and a code example.**

We covered the fundamentals of using Esfinge Query Builder and its polyglot annotations:
- @PersistenceType
- @PolyglotOneToMany
- @PolyglotJoin

Please review C1) subsection if necessary while carrying out the proposed tasks in the experiment.

## D) Experiment

You must complete two tasks that involve coding in both approaches presented.

There are four task groups; complete each task from only one of them.

**Open the tasks from the defined group, understand what is asked and mark the start and end time of your coding for each one.**

A detailed description of each task is provided below.

### Start ⏳

To access the description of each task, click below:

**Group A**
- [**Conventional1**](https://github.com/PolyglotExperiment/Conventional1)
- [**Polyglot2**](https://github.com/PolyglotExperiment/Polyglot2)

**Group B**
- [**Conventional2**](https://github.com/PolyglotExperiment/Conventional2)
- [**Polyglot1**](https://github.com/PolyglotExperiment/Polyglot1)

**Group C**
- [**Polyglot1**](https://github.com/PolyglotExperiment/Polyglot1)
- [**Conventional2**](https://github.com/PolyglotExperiment/Conventional2)

**Group D**
- [**Polyglot2**](https://github.com/PolyglotExperiment/Polyglot2)
- [**Conventional1**](https://github.com/PolyglotExperiment/Conventional1)

### Finish ☑️
After completing the tasks, please 🎯 **complete the questions provided at the** [https://forms.gle/QwuAV6opgTsqCDK69](https://forms.gle/QwuAV6opgTsqCDK69).




