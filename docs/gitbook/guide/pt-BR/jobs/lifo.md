---
description: 'Last-in, First Out'
---

# LIFO

Em alguns casos, é útil processar os *jobs* no modo LIFO \(Last-in, First-Out, *último a entrar, primeiro a sair em português*\). Isso significa que os *jobs* mais recentes adicionados à fila serão processados antes dos mais antigos.

```typescript
import { Queue } from 'bullmq'

const myQueue = new Queue('Paint');

// Adicione um job que será processado antes de todos os outros
await myQueue.add('wall', { color: 'pink' }, { lifo: true });
```

 

