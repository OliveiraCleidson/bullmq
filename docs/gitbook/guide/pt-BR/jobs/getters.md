# Getters

Quando os *jobs* são adicionados a uma fila, eles terão estados diferentes durante sua vida útil. O BullMQ fornece métodos para recuperar informações e *jobs* com estados diferentes.



![](../../../.gitbook/assets/image.png)

#### Contagem de Job

Muitas vezes, é necessário saber quantos *jobs* estão em um determinado estado:

```typescript
import { Queue } from 'bullmq'

const myQueue = new Queue('Paint');

const counts = await myQueue.getJobCounts('wait', 'completed', 'failed');

// Retorna um objeto como este { wait: number, completed: number, failed: number }
```

Os estados disponíveis são: _completed, failed, delayed, active, wait, paused_ e _repeat._

#### Get Jobs

Também é possível recuperar os *jobs* com paginação. Por exemplo:

```typescript
const completed = await myQueue.getJobs(['completed'], 0, 100, true);

// retorna os 100 jobs mais antigos
```



