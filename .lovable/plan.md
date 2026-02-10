

# Agenda com Intervalos de 15 Minutos + Filtro de Horario

## O que muda para voce
- Os horarios da agenda passam a ser exibidos de **15 em 15 minutos** (07:00, 07:15, 07:30, 07:45, 08:00...)
- O intervalo total vai das **07:00 ate as 23:00**
- Um novo botao na barra de ferramentas permite alternar entre **"Todos os horarios"** (7h-23h) e **"Somente horario comercial"** (apenas quando a barbearia esta aberta)

## Como vai funcionar
1. Cada linha do calendario representa 15 minutos em vez de 1 hora
2. O label de horario aparece a cada 15 min: 07:00, 07:15, 07:30, 07:45, 08:00...
3. A altura de cada slot sera menor para caber mais linhas na tela
4. Um botao com icone de relogio no CalendarHeader alterna entre mostrar todos os horarios ou apenas o horario de funcionamento
5. Agendamentos continuam sendo posicionados no slot correto baseado no horario de inicio

---

## Detalhes Tecnicos

### Arquivos alterados

**1. `src/components/agenda/CalendarWeekView.tsx`**
- Trocar geracao do array `HOURS` para gerar slots de 15 em 15 min (objetos `{ hour, minute }`)
- Range padrao: 7:00 ate 22:45 (totalizando 64 slots)
- Modo "somente horario comercial": filtra slots para mostrar apenas dentro do horario de abertura/fechamento
- Ajustar `DEFAULT_HOUR_HEIGHT` para ~24px por slot de 15 min (em vez de 80px por hora)
- Atualizar mapeamento de appointments para considerar hora + minuto
- Atualizar labels para mostrar HH:MM a cada slot
- Ajustar posicao do indicador de horario atual

**2. `src/components/agenda/CalendarDayView.tsx`**
- Mesmas mudancas: slots de 15 min, range 7-23h
- Atualizar `HOURS` para array de `{ hour, minute }`
- Ajustar altura dos slots e mapeamento de agendamentos
- Atualizar labels e indicador de horario

**3. `src/components/agenda/CalendarHeader.tsx`**
- Adicionar nova prop `showBusinessHoursOnly` + `onToggleBusinessHours`
- Novo botao com icone `Clock` para alternar entre "Todos os horarios" e "Horario comercial"
- Tooltip explicando o estado atual

**4. `src/pages/Agenda.tsx`**
- Adicionar estado `showBusinessHoursOnly` (salvo no localStorage)
- Passar novas props para CalendarHeader, CalendarWeekView e CalendarDayView

### Logica de mapeamento de agendamentos
- Cada agendamento sera posicionado no slot de 15 min mais proximo (ex: agendamento as 10:20 vai no slot 10:15)
- A chave do mapeamento muda de `hour` para `hour:minute` (ex: "10:15")

### Impacto visual
- Com 64 slots (7h-23h), a agenda tera scroll vertical significativo no modo "todos os horarios"
- No modo "horario comercial" (ex: 9h-19h = 40 slots), sera mais compacto
- O modo compacto existente continuara funcionando, ajustando a altura dos slots para caber na tela
