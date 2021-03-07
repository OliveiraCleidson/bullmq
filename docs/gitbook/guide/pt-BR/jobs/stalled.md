# Stalled

{% hint style="info" %}
As verificações dos *stalled jobs* só funcionarão se houver pelo menos uma instância da classe [`QueueScheduler`](../queuescheduler.md) configurada na Fila.
{% endhint %}

Quando um *job* está em um estado ativo, ou seja, está sendo processado por um *worker*, ele precisa atualizar continuamente a fila para notificar que o *worker* ainda está trabalhando no *job*. Esse mecanismo evita que um *worker* que trave ou entre em um loop infinito mantenha um *job* no estado ativo para sempre.

Quando um *worker* não consegue notificar a fila de que ainda está processando um determinado *job*, esse *job* é movido de volta para a lista de espera ou para o conjunto de *jobs* com falha. Em seguida, dizemos que o *job* foi interrompido e a fila emitirá o evento *'stalled'*.

{% hint style="info" %}
Não há um estado *'stalled'*, apenas um evento *'stalled'* emitido quando um trabalho é automaticamente movido do estado ativo para o estado de espera.
{% endhint %}

Para evitar *stalled jobs*, certifique-se de que seu *worker* não mantenha o *event loop* do Node.js muito ocupado, a duração padrão máxima de uma verificação de *jobs* interrompidos é de 30 segundos, contanto que você não execute operações de CPU que excedam esse valor, você não deve obter *stalled jobs*.

Outra maneira de reduzir a chance de *stalled jobs* é usar os chamados processadores "sandbox". Nesse caso, os *workers* gerarão novos processos separados do Node.js, executados separadamente do processo principal.

{% code title="main.ts" %}
```typescript
import { Worker } from 'bullmq';

const worker = new Worker('Paint', painter);
```
{% endcode %}

{% code title="painter.ts" %}
```typescript
export default = (job) => {
    // Pinta algo
}
```
{% endcode %}

