<template>
  <div class="calendar-container">
    <h1>Calendário</h1>
    <div class="calendar">
      <div class="header">
        <button @click="prevMonth">←</button>
        <span>{{ monthNames[currentMonth] }} {{ currentYear }}</span>
        <button @click="nextMonth">→</button>
      </div>
      <div class="days">
        <div v-for="day in days" :key="day" class="day">
          {{ day }}
        </div>
      </div>
      <div class="dates">
        <div
            v-for="date in calendarDates"
            :key="date.dateString"
            class="date"
        >
          <span>{{ date.date }}</span>
          <span v-if="date.eventCount > 0" class="event-count">{{ date.eventCount }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';

const monthNames = ["Janeiro", "Fevereiro", "Março", "Abril", "Maio", "Junho", "Julho", "Agosto", "Setembro", "Outubro", "Novembro", "Dezembro"];
const currentDate = new Date();
const currentMonth = ref(currentDate.getMonth());
const currentYear = ref(currentDate.getFullYear());
const events = ref([]); // Armazena os eventos buscados

const days = ['D', 'S', 'T', 'Q', 'Q', 'S', 'S'];

const fetchEvents = async () => {
  // Simulação de uma chamada assíncrona para buscar eventos de um servidor
  return new Promise((resolve) => {
    setTimeout(() => {
      // Exemplo de eventos para dias
      const simulatedEvents = [
        { date: '2024-09-05', count: 2 },
        { date: '2024-09-12', count: 1 },
        { date: '2024-09-20', count: 3 },
      ];
      resolve(simulatedEvents);
    }, 1000); // Simula uma latência de 1 segundo
  });
};

const loadEvents = async () => {
  events.value = await fetchEvents();
};

const getFirstDayOfMonth = (month: number, year: number) => {
  return new Date(year, month, 1).getDay();
};

const getDaysInMonth = (month: number, year: number) => {
  return new Date(year, month + 1, 0).getDate();
};

const calendarDates = computed(() => {
  const firstDay = getFirstDayOfMonth(currentMonth.value, currentYear.value);
  const daysInMonth = getDaysInMonth(currentMonth.value, currentYear.value);

  const dates = [];

  // Adiciona os dias vazios antes do primeiro dia do mês
  for (let i = 0; i < firstDay; i++) {
    dates.push({ date: '', eventCount: 0 });
  }

  // Adiciona os dias do mês
  for (let day = 1; day <= daysInMonth; day++) {
    const dateString = `${currentYear.value}-${String(currentMonth.value + 1).padStart(2, '0')}-${String(day).padStart(2, '0')}`;
    const eventCount = events.value.find(event => event.date === dateString)?.count || 0;
    dates.push({ date: day, eventCount });
  }

  return dates;
});

const prevMonth = () => {
  if (currentMonth.value === 0) {
    currentMonth.value = 11;
    currentYear.value--;
  } else {
    currentMonth.value--;
  }
};

const nextMonth = () => {
  if (currentMonth.value === 11) {
    currentMonth.value = 0;
    currentYear.value++;
  } else {
    currentMonth.value++;
  }
};

// Carrega os eventos ao montar o componente
onMounted(() => {
  loadEvents();
});
</script>

<style scoped>
.calendar-container {
  background-color: #121212; /* Cor de fundo escuro */
  color: #ffffff; /* Cor do texto claro */
  padding: 20px;
  border-radius: 8px;
  max-width: 500px;
  margin: auto;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  text-align: center;
}

.day {
  font-weight: bold;
  color: #e0e0e0; /* Cor clara para os dias da semana */
}

.dates {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  text-align: center;
}

.date {
  padding: 10px;
  border: 1px solid #333; /* Borda escura */
  border-radius: 4px;
  background-color: #1e1e1e; /* Cor de fundo dos dias */
  position: relative;
  transition: background-color 0.2s;
}

.date:hover {
  background-color: #3a3a3a; /* Cor de fundo ao passar o mouse */
}

.event-count {
  background-color: #007BFF; /* Cor de fundo do contador de eventos */
  color: white;
  border-radius: 50%;
  padding: 3px 6px;
  position: absolute;
  top: 5px;
  right: 5px;
  font-size: 12px;
}
</style>
