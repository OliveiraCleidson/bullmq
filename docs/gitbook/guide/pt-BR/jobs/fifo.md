---
description: 'First-In, First-Out'
---

# FIFO

O primeiro tipo de *job* que vamos descrever é o tipo FIFO \(First-In, First-Out, *Primeiro a entrar, primeiro a sair em português*\). Este é o tipo padrão ao adicionar um *job* em uma fila. Está ordem é preservada independente da quantidade de processadores que você tem, no entanto, se você tiver mais de um *worker* ou concorrência maior que 1, mesmo que os *workers* iniciem os *jobs* na ordem inserida, eles podem ser concluídos em uma ordem levemente diferente, já que alguns *jobs* podem levar mais tempo para serem conclúidos do que outros. 

```typescript
import { Queue } from 'bullmq'

const myQueue = new Queue('Paint');

// Adicione um job que será processado antes de todos os outros
await myQueue.add('wall', { color: 'pink' });
```

Ao adicionar *jobs* à fila, existem várias opções que você pode usar. Por exemplo, você pode especificar quantos *jobs* deseja manter quando os *jobs* forem concluídos ou falharem:

```typescript
await myQueue.add(
  'wall',
  { color: 'pink' },
  { removeOnComplete: true, removeOnFail: 1000 },
);
```

No exemplo acima, todos os *jobs* concluídos serão removidos automaticamente e os últimos 1000 *jobs* que falharam serão mantidos na fila.

## Opções de jobs padrão

Frequentemente, você desejará fornecer as mesmas opções de *job* para todos os *jobs* que adicionar à Fila. Nesse caso, você pode usar a opção "defaultJobOptions" ao instanciar a classe Queue:

```typescript
const queue = new Queue('Paint', { defaultJobOptions: {
  removeOnComplete: true, removeOnFail: 1000
});
```

