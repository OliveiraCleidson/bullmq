# Prioritized

Os *jobs* também podem possuir uma opção de prioridade. Usando prioridades, a ordem de processamento do *job* será afetada pela prioridade especificada em vez de seguir um padrão FIFO ou LIFO.

{% hint style="warning" %}
Adicionar *jobs* priorizados é uma operação mais lenta do que os outros tipos de *jobs*, com uma complexidade O\(n\) relativa ao número de *jobs* na Queue.
{% endhint %}

As prioridades vão de 1 a MAX\_INT, enquanto o número mais baixo sempre tem prioridade mais alta do que os números mais altos.

```typescript
import { Queue } from 'bullmq'

const myQueue = new Queue('Paint');

await myQueue.add('wall', { color: 'pink' }, { priority: 10 });
await myQueue.add('wall', { color: 'brown' }, { priority: 5 });
await myQueue.add('wall', { color: 'blue' }, { priority: 7 });

// A parede será pintada primeiro de marrom, depois azul e
// finalmente rosa.


```

