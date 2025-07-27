[//]: # (## Welcome to the place where clean architecture is made easy.)

[//]: # (💡 Discover our artifacts. Understand our vision. Join the force.)

> ⚠️ This README is a WIP. Soon it will be complete and in english as well.

## A SDK

CAE — _Clean Arch Enablers_ é a SDK que tem como objetivo habilitar as vantagens da **Arquitetura Limpa** e neutralizar ao máximo a fricção de implementá-la. Para um bom entendimento a respeito dos componentes desta SDK, software deve ser visto como uma entidade que existe exclusivamente para **servir funcionalidades**, e essas funcionalidades são categorizadas como os **Casos de Uso** de tal entidade. Ou seja, são o _para-quê_ de um software existir. 

Vale frisar que esse recorte não é sobre _qualquer_ funcionalidade de uma aplicação, mas sim sobre as que servem de **_ponto de acesso ao domínio_** sendo implementado. Existem funcionalidades que são **Casos de Uso** de um domínio, e funcionalidades que são passos internos de um **Caso de Uso**. É importante saber distinguir cada qual.

<br>

![use-cases-vs-inner-steps](drawings/use-case-vs-inner-step.svg)

<br>

Dado este primeiro passo, o próximo é entender que todo **Caso de Uso** é uma ação que possui início, meio e fim. O _**início**_ pode receber ou não dados de entrada; o _**meio**_ é a lógica intrínseca da ação; o _**fim**_ pode ou não retornar dados de saída. Na ótica do CAE, não importa qual aplicação um time esteja criando, todas terão esta mesma característica: ser um artefato com pontos de acesso que são ações com início, meio e fim. Este fato não depende de _**como**_ as funcionalidades estão sendo servidas: não importa se estão sendo expostas como _REST Endpoints_, _Kafka Consumers_ ou _CRON Jobs_... independente do meio de exposição, todo tipo de ponto de acesso ao domínio pode ser abstraído em formato de **Caso de Uso**.

<br>

![flows](drawings/flows.svg)

<br>

## ``UseCase`` e seus subtipos

O modelo de pensamento orientado a **Caso de Uso** foi traduzido para 4 componentes básicos da SDK:

### ``FunctionUseCase<I, O>``
```java
//Funcionalidades que recebem dados de entrada, executam seu algoritmo e terminam retornando dados de saída
public interface FunctionUseCase<I, O> extends UseCase {
    O execute(I input);
}
```

### ``ConsumerUseCase<I>``
```java
//Funcionalidades que recebem dados de entrada, executam seu algoritmo e terminam
public interface ConsumerUseCase<I> extends UseCase {
    void execute(I input);
}
```

### ``SupplierUseCase<O>``
```java
//Funcionalidades que executam seu algoritmo e terminam retornando dados de saída
public interface SupplierUseCase<O> extends UseCase {
    O execute();
}
```

### ``RunnableUseCase``
```java
//Funcionalidades que executam seu algoritmo e terminam
public interface RunnableUseCase extends UseCase {
    void execute();
}
```

Isso foi desenhado para que qualquer possível funcionalidade se encaixe em 1 tipo de ``UseCase``.

Outro axioma notável é que casos de uso que recebem dados de entrada normalmente precisam verificá-los antes. Isso também foi abstraído para dentro de um outro componente básico:

### ``UseCaseInput``
```java
//Modelos de input com comportamento nativo de verificação
public class UseCaseInput {
    public void autoverify() { ... }
}
```

Com isso todo ``UseCase`` que aceita dados de entrada requer que seu modelo de input seja um derivado de ``UseCaseInput``, tornando possível contar com a API padrão ``UseCaseInput.autoverify(): void``. O ``Autoverify`` irá escanear a si mesmo e assegurar antes da execução de seu ``UseCase`` que:

- Todos os campos marcados com ``@NotNullInputField`` não estão ``null`` (_serve para qualquer tipo de dado_).
- Todos os campos marcados com ``@NotEmptyInputField`` não estão vazios (_serve para coleções e ``String``_).
- Todos os campos marcados com ``@NotBlankInputField`` não estão ``null`` (_serve para ``String`` e coleções de ``String``_).
- Todos os campos marcados com ``@ValidInnerProperties`` estão recursivamente complacentes com as 3 anotações mencionadas acima (_serve para tipos customizados_).

Com isso o ``FunctionUseCase`` e o ``ConsumerUseCase`` ficam assim:

```java
//Input precisa ser subtipo de UseCaseInput
public interface FunctionUseCase<I extends UseCaseInput, O> extends UseCase { ... }

//Input precisa ser subtipo de UseCaseInput
public interface ConsumerUseCase<I extends UseCaseInput> extends UseCase { ... }
```

A intenção do design é que se payloads de input não respeitarem o contrato, uma exception seja lançada e o ``UseCase`` não seja executado. Porém... do jeito até então apresentado, a responsabilidade de chamar o ``UseCaseInput::autoverify`` seria do desenvolvedor, dentro da implementação de seu ``UseCase``. Isso não foi considerado flexibilidade, mas sim brecha. A flexibilidade já é garantida ao deixar que o desenvolvedor escolha colocar ou não anotações nos campos, mas a responsabilidade de garantir que as anotações sejam respeitadas uma vez declaradas no contrato não faz sentido deixar solto. Isso é algo que todo caso de uso que aceita input deveria fazer automaticamente.

Daí nasce a primeira ``Autofeature``, funcionalidades que são internas e automaticamente garantidas a qualquer tipo de ``UseCase``, independente da regra de negócio de uma implementação específica. É então para habilitar esta primeira ``Autofeature`` e preparar o terreno para próximas, que os tipos de ``UseCase`` com input deixam de ser interfaces: com a utilização de _Template Pattern_, cada tipo se torna uma **classe abstrata** com implementações padrões que giram em torno de uma regra de negócio abstrata, que só será implementada posteriormente, via polimorfismo. 

Fica assim:

### ``FunctionUseCase<I, O>``
```java
public abstract class FunctionUseCase<I extends UseCaseInput, O> extends UseCase {
    
    /*
    API do caso de uso. Método concreto. 
    Chama a Autofeature e em seguida a lógica do caso de uso.                             
    */
    public O execute(I input){
        input.autoverify();
        return this.applyInternalLogic(input);
    }
    
    /*
    método abstrato que encapsulará a lógica específica 
    de cada caso de uso      
    */
    protected abstract O applyInternalLogic(I input); 
    
}
```

### ``ConsumerUseCase<I>``
```java
public abstract class ConsumerUseCase<I extends UseCaseInput> extends UseCase {

    /*
    API do caso de uso. Método concreto. 
    Chama a Autofeature e em seguida a lógica do caso de uso.                             
    */
    public void execute(I input){
        input.autoverify();
        this.applyInternalLogic(input);
    }

    /*
    método abstrato que encapsulará a lógica específica 
    de cada caso de uso      
    */
    protected abstract void applyInternalLogic(I input);

}
```

Desta forma o desenvolvedor não precisa mais se preocupar em chamar o ``UseCaseInput::autoverify`` para que a verificação do payload de input aconteça. Se torna automático, uma ``Autofeature``.

No caso do ``Autoverify``, como já mencionado, caso o payload de entrada não respeite o contrato definido pelo desenvolvedor, uma exception será lançada, e o ``UseCase`` não terá sua lógica interna executada. A exception que esta ``Autofeature`` lança é do tipo ``MappedException``, outro componente básico da SDK.

<br>

## ``MappedException`` e seus subtipos

Tipos de ``MappedException`` servem para abstrair cenários de exceção comuns, que acontecem em qualquer aplicação:


### ``InternalMappedException``
```java
//exceptions provocadas por problemas internos da aplicação
public class InternalMappedException extends MappedException {}
```

### ``InputMappedException``
```java
//exceptions provocadas por dados de entrada
public class InputMappedException extends MappedException {}
```

### ``NotFoundMappedException``
```java
//exceptions provocadas por não encontrar algo que era esperado
public class NotFoundMappedException extends MappedException {}
```

### ``NotAuthorizedMappedException``
```java
//exceptions provocadas por ausência de autorização numa ação protegida
public class NotAuthorizedMappedException extends MappedException {}
```

### ``NotAuthenticatedMappedException``
```java
//exceptions provocadas por ausência de identificação numa ação protegida
public class NotAuthenticatedMappedException extends MappedException {}
```

Com esse componente da SDK, tipos comuns de exceção são disponibilizados para reuso, bastando aplicá-los em cenários condizentes. Isso incentiva o uso de exceptions semânticas, sem ter a fricção de criá-las manualmente. 

Outro efeito derivado do uso desse componente é que padrões são estabelecidos, fomentando previsibilidade não só na SDK em si como também nas suas aplicações clientes. Por exemplo, tipos de ``MappedException`` sempre serão interpretados pela SDK como **cenários de erro intencionalmente mapeados**: é considerado que desenvolvedores fizeram a escolha consciente de lançar esses tipos de exception como parte do fluxo. Portanto, sempre que uma exception é interceptada e ela é do tipo ``MappedException``, ela será propagada adiante sem interferência por parte da SDK.

<br>

![mapped-exceptions](drawings/mapped-exception.svg)
Contudo, mesmo que times tenham o cuidado de utilizar subtipos de ``MappedException``, ainda assim é possível que erros inesperados aconteçam: exceptions  que uma biblioteca lança, _edge cases_ na própria lógica do desenvolvedor que causam ``NullPointerException``... tudo pode acontecer. Isso deixa em evidência mais um padrão, que é endereçado por outro componente básico da SDK: ``Trier``.

<br>

## ``Trier`` e suas APIs
O ``Trier`` é como um bloco de ``try-catch``: nele é possível encapsular qualquer tipo de ação e parametrizar um tratamento específico em caso dela lançar uma exception **inesperada**, ou seja, diferente de ``MappedException``. O tratamento parametrizável tem o objetivo de transformar tais exceptions inesperadas em instâncias de ``MappedException``, na intenção de controlar o caos e preservar a ordem.

A API mais básica do ``Trier`` é essa:

```java
/*
Tenta executar 'someString.concat("another string")'
e se isso der qualquer problema diferente de MappedException, 
irá converter para InternalMappedException.
*/
Trier.of(() -> someString.concat("another string")) //<- encapsula ação
    .onUnexpectedExceptions(ex -> new InternalMappedException("problem at blablabla...", ex)) //<- parametriza handler
    .execute(); //<- executa ação pronto para usar o handler
```

O ``Trier`` pode encapsular qualquer tipo de ação:

- ações com entrada e saída
- ações com entrada e sem saída
- ações sem entrada e com saída
- ações sem entrada nem saída


Além de parametrizar um _handler_ para transformar exceptions inesperadas em ``MappedException``, é possível também definir quais cenários são elegíveis para **retentativas** em caso da ação quebrar.

Funciona assim:

```java

Trier.of(() -> this.doSomethingWith(thatThing))
        .retryOn(IOException.class, 5, 300, TimeUnit.MILLISECONDS)//<-- retenta até 5x em caso de IOException
        .retryOn(ServiceUnavailableException.class, 10, 100, TimeUnit.MILLISECONDS)//<-- retenta até 10x em caso de ServiceUnavailableException
        .onUnexpectedExceptions(this::handleUnexpectedException)
        .execute();

```

A API ``retryOn`` recebe os seguintes parâmetros:

- a exception que se ocorrer, retentativas devem ser executadas.
- até quantas vezes para essa exception a ação pode retentar.
- tempo base para começar a retentar.

O tempo base vai servir como tempo de espera entre a tentativa que falhou e a primeira retentativa após. Uma vez a primeira retentativa também tendo falhado, as próximas irão acontecer com intervalos de tempo diferentes. A fórmula é a seguinte:

Para o cenário de até 10 retentativas, com tempo base de 300ms:

- tentativa falha: espera **300**ms (_300 * 2^0_).
- 1ª retentativa falha: espera **600**ms (_300 * 2^1_).
- 2ª retentativa falha: espera **1200**ms (_300 * 2^2_).
- 3ª retentativa falha: espera **2400**ms (_300 * 2^3_).
- 4ª retentativa falha: espera **4800**ms (_300 * 2^4_).
- 5ª retentativa falha: espera **9600**ms (_300 * 2^5_).
- 6ª retentativa falha: espera **19200**ms (_300 * 2^6_).
- 7ª retentativa falha: espera **38400**ms (_300 * 2^7_).
- 8ª retentativa falha: espera **76800**ms (_300 * 2^8_).
- 9ª retentativa falha: espera **153600**ms (_300 * 2^9_).
- 10ª retentativa falha. Lança ``NoRetriesLeftException``.

Sendo possível concluir que o setup de até 10x retentativas com tempo base de 300ms possui potencial para levar até 306900ms, isto é, até aproximadamente 5 minutos (soma de todas as esperas).

A fórmula é a seguinte:

```tempo de espera = tempo base * 2 ^ (número de retentativas já executadas)```

Além da API do ``Trier`` possibilitar o setup de vários cenários distintos para retentativa numa mesma ação, ela também disponibiliza um método para setup de _handler_ em caso de exaustão. Ou seja, para casos de esgotamento de retentativas. No exemplo dado acima, tal _handler_ seria invocado no momento que a 10ª retentativa teria falhado.

É assim:

```java
Trier.of(() -> this.doSomethingWith(thatThing))
        .retryOn(IOException.class, 5, 300, TimeUnit.MILLISECONDS)
        .retryOn(ServiceUnavailableException.class, 10, 100, TimeUnit.MILLISECONDS)
        .onExhaustion(failureStatus -> this.handleExhaustion(failureStatus))//<- é executado em caso de esgotar retentativas
        .onUnexpectedExceptions(this::handleUnexpectedException)
        .execute();
```

O tipo de _failureStatus_ é ``FailureStatus``, que possui os seguintes campos:

- exception: a exception responsável por ter causado a exaustão do ``Trier`` para a ação.
- totalOfRetries: quantas tentativas aconteceram para que a exaustão tenha ocorrido.

Daí cabe ao desenvolvedor escolher o que fazer nesse cenário.

Mais uma conveniência que o ``Trier`` possibilita é que sempre quando uma retentativa acontece, o desenvolvedor pode criar um ``RetrySubscriber`` para receber uma notificação e reagir como quiser a ela (gravar logs, enviar métricas, etc.).

Para declarar um _subscriber_, basta criar uma classe que implemente a interface ``RetrySubscriber``:

```java

public class MyOwnRetrySubscriber implements RetrySubscriber{
    
    @Override
    public void receiveRetryNotification(RetryNotification retryNotification){
        //gravar logs, extrair métricas, etc.
    }
    
}
```
O objeto recebido, do tipo ``RetryNotification``, possui os seguintes campos:

- cause: exception que causou a retentativa.
- totalOfRetriesAtThisPoint: quantas retentativas já ocorreram para esse cenário até o momento da notificação.

Para que o _subscriber_ seja utilizado pela SDK, é necessário que ele seja fornecido para o ``RetryNotifier``:

```java

RetryNotifier.SINGLETON.subscribe(new MyOwnRetrySubscriber());

```

É crucial que a implementação de ``RetrySubscriber`` seja fornecida para o ``RetryNotifier`` em tempo de bootstrap da aplicação cliente, para que a instância já esteja presente em caso de qualquer retentativa ocorrer.

O ``RetryNotifier`` emite as notificações em seu próprio _Pool_ de _Threads_, significando que se uma retentativa ocorre na _Thread_ de uma requisição sendo atendida pela aplicação cliente e ela está preparada para receber notificações, este recebimento acontecerá em outra _Thread_, de forma não bloqueante.

É possível personalizar a _Pool_ de _Threads_ usada pelo ``RetryNotifier`` por meio do componente ``RetryNotifierThreadPoolProvider``:

```java
RetryNotifierThreadPoolProvider.SINGLETON
        .setMinSize(3)//<- tamanho mínimo da Pool de Threads
        .setMaxSize(15)//<- tamanho máximo para a Pool
        .setKeepAliveTimeForIdleThreadsInSeconds(4)//<- limite de segundos 1 thread extra pode ficar viva e ociosa
        .setQueueCapacity(10)//<- quantas notificações podem ser enfileiradas antes de começar a escalar as Threads
        .setPoolName("MyCustomRetryNotifierPool");//<- nome da Pool

```

Assim como a inscrição de um ``RetrySubscriber``, é importante que customizações como essa da _Pool_ de _Threads_ seja realizada em tempo de bootstrap da aplicação cliente.

Caso o desenvolvedor não forneça personalizações para a _Pool_, esses são os valores _default_:

- minSize: 5.
- maxSize: 30.
- keepAliveTimeForIdleThreadsInSeconds: 60.
- queueCapacity: 100.
- poolName: _CaeRetryNotifierThreadPool_.

<br>

> ⚠️ CONTINUE...

<br>

<br>


## ⚡Joining the force

If anyone gets interested in becoming an official collaborator, it is very simple. The workflow is:

1. 🔍 Identify where it would be interesting to collaborate.
2. 📝 Open a new issue on the repository that has an opportunity for enhancement, specifying the intended changes.
3. 🛠️ Create a new branch to address the changes (allowed prefixes: _feature/_, _refact/_, _docs/_, or _fix/_).
5. 📩 Once it is done, open a new pull request.
6. 🔀 Pass through the code review phase.
7. ✅ Welcome to the team!

Once completed the workflow, the developer gets associated with our GitHub and LinkedIn organization pages.

<br>
<br>
<br>
<br>

<p align="center">
  CAE — Clean Architecture made easy.
</p>
