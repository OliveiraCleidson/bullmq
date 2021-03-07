# Delayed

Os *jobs* adicionados a uma fila normalmente serão processados rapidamente enquanto um *worker* estiver disponível para despachá-los. No entanto, também é possível adicionar um parâmetro de *delay* para que os *jobs* esperem pelo menos esse período de tempo antes de serem processados. Porém isso não garante que o *job* será processado no momento seguinte ao *delay*, depende do quão ocupada a fila está após o *delay* e quantos outros *delayed jobs* estão programados naquele exato momento.

{% hint style="info" %}
Os *delayed jobs* só serão processados se houver pelo menos uma instância de [`QueueScheduler`](../queuescheduler.md) configurada na fila.
{% endhint %}

Este é um exemplo de como adicionar jobs atrasados:

```typescript
import { Queue, QueueScheduler } from 'bullmq'

const myQueueScheduler = new QueueScheduler('Paint');
const myQueue = new Queue('Paint');

// Adiciona um job que terá um delay de pelo menos 5 segundos.
await myQueue.add('house', { color: 'white' }, { delay: 5000 });

```

