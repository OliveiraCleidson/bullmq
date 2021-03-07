# Repeatable

Existe um tipo especial de _meta_ *job* chamado de **repeatable**. Esses *jobs* são especiais no sentido de que, embora você só adicione um *job* à fila, eles continuarão se repetindo de acordo com uma programação predefinida.

Cada vez que um *repeatable job* é selecionado para processamento, o próximo *repeatable job* é adicionado à fila com um *delay* adequado. Os *repeatable job*, portanto, nada mais são do que *delayed jobs* adicionados à fila de acordo com algumas configurações.

{% hint style="info" %}
*Repeatable job* são apenas *delayed jobs*, portanto, você também precisa de uma instância da classe QueueScheduler para agendar os *jobs* de acordo.
{% endhint %}

Existem duas maneiras de especificar um padrão de repetição de *job*, com uma expressão cron \(usando [cron-parser](https://www.npmjs.com/package/cron-parser) no formato "unix cron c/ segundos opcionais"\), ou especificando uma quantidade fixa de milissegundos entre as repetições.

```typescript
import { Queue, QueueScheduler } from 'bullmq'

const myQueueScheduler = new QueueScheduler('Paint');
const myQueue = new Queue('Paint');

// Repete o job uma vez todos os dias às 3:15 (am)
await myQueue.add('submarine', { color: 'yellow' }, 
  {
    repeat: {
      cron: '* 15 3 * * *'
    }
  });

// Repete o job a cada 10 segundos, mas não mais do que 100 vezes
await myQueue.add('bird', { color: 'bird' }, 
  {
    repeat: {
      every: 10000,
      limit: 100
    }
  });
```

Existem algumas considerações importantes sobre *jobs* repetíveis:

* Bull é inteligente o suficiente para não adicionar o mesmo *repeatable job* se as opções de repetição forem as mesmas.
* Se não houver *workers* em execução, os *repeatable jobs* não serão acumulados na próxima vez em que um *worker* estiver disponível.
* *Repeatable Jobs* podem ser removidos usando o método [removeRepeatable](https://github.com/taskforcesh/bullmq/blob/master/docs/gitbook/api/bullmq.queue.removerepeatable.md) ou [removeRepeatableByKey](https://github.com/taskforcesh/bullmq/blob/master/docs/gitbook/api/bullmq.queue.removerepeatablebykey.md).

Todos os *repeatable jobs* têm uma *job key* que contém alguns metadados do próprio *job*. É possível recuperar todos os atuais *repeatable jobs* na chamada de fila [getRepeatableJobs](https://github.com/taskforcesh/bullmq/blob/master/docs/gitbook/api/bullmq.queue.getrepeatablejobs.md):

```typescript
import { Queue } from 'bullmq'

const myQueue = new Queue('Paint');

const repeatableJobs = await myQueue.getRepeatableJobs();

```

Uma vez que os *repeatable jobs* são *delayed jobs*, a repetição é obtida gerando um novo *delayed job* precisamente antes do início do processamento do *job* atual. Os *jobs* exigem IDs exclusivos que evitam duplicidade, o que implica que a opção padrão do jobId não funcione da mesma forma em que funciona nos *jobs* normais. Com *repeatable jobs*, o jobId é usado para gerar os IDs únicos, por exemplo, se você tiver 2 *repeatable jobs* com o mesmo nome e opções, você poderá usar o jobId para ter 2 *repeatable jobs* diferentes:

```typescript
import { Queue, QueueScheduler } from 'bullmq'

const myQueueScheduler = new QueueScheduler('Paint');
const myQueue = new Queue('Paint');

// Repete o job a cada 10 segundos, mas não mais do que 100 vezes
await myQueue.add('bird', { color: 'bird' }, 
  {
    repeat: {
      every: 10000,
      limit: 100
    },
    jobId: "colibri"
  });

await myQueue.add('bird', { color: 'bird' }, 
  {
    repeat: {
      every: 10000,
      limit: 100
    },
    jobId: "pingeon"
  });
  
  
```

## Slow repeatable jobs

Vale a pena mencionar o caso em que a frequência de repetição é maior do que o tempo que leva para processar um *job*.

Por exemplo, digamos que você tenha um *job* que se repete a cada segundo, mas o processo do *job* em si leva 5 segundos. Conforme explicado acima, os *repeatable jobs* são apenas *delayed jobs*, então isso significa que o próximo *repeatable job* será adicionado assim que o próximo *job* começar a ser processado.

Neste exemplo específico, o *worker* pegará o próximo *job* e também adicionará o próximo *repeatable job* com atraso de 1 segundo, pois esse é o intervalo de repetição. O *worker* precisará de 5 segundos para processar o *job* e, se houver apenas 1 *worker* disponível, o próximo *job* precisará aguardar 5 segundos completos antes de ser processado.

Por outro lado, se houvesse 5 *workers* disponíveis, eles provavelmente seriam capazes de processar todos os *repeatable jobs* repetíveis com a frequência desejada de um trabalho por segundo.

