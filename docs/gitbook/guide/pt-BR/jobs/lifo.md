---
description: 'Last-in, First Out'
---

# LIFO

Em alguns casos, é útil processar os *jobs* no modo LIFO \(Last-in, First-Out, *último a entrar, primeiro a sair em português*\). Isso significa que os *jobs* adicionados recentemente à fila serão processados antes dos outros mais antigos.

```typescript
import { Queue } from 'bullmq'

const myQueue = new Queue('Paint');

// Adiciona um job que será processado antes de todos os outros
await myQueue.add('wall', { color: 'pink' }, { lifo: true });
```

 

