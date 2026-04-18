<script setup>
import { ref, computed } from 'vue';
import Table from '@/components/molecules/Table.vue';
import { players, groups } from '@/mocks';
import Group from '@/components/molecules/Group.vue';
import Button from '@/components/atoms/Button.vue';
import { onBeforeRouteLeave } from 'vue-router';

/** Максимальное количество человек в группе */
const MAX_PLAYERS_GROUP = 3;

/** Все игроки со сформированным ФИО и отсортированные */
const people = computed(() =>
  players
    .map((p, index) => ({...p, fio: `${p.surname} ${p.name}`}))
    .sort((a,b) => a.fio.localeCompare(b.fio))
);

const playersGroups = ref([]);
const noGroupPlayers = computed(() => 
  people.value.filter(person =>
    !playersGroups.value.some(group => group.player_id === person.id)
  )
);

/** 
 * Распределение игроков по группам 
 * @returns {{
 * [group_id: string]: Array<{
 *  player_id: number;
 *  fio: string;
 * }
 * }}
*/
const groupPlayers = computed(() => {
  const grouped = Object.groupBy(playersGroups.value, p => p.group_id);

  return Object.fromEntries(
    Object.entries(grouped).map(([groupId, players]) => [
      groupId,
      players.map(p => ({
        player_id: p.player_id,
        fio: people.value.find(item => item.id === p.player_id)?.fio
      }))
    ])
  );
});

/** Проверка на заполненность всех групп */
const allGroupsFull = computed(() => {
  return groups.every(group => {
    const count = playersGroups.value.filter(p =>
      p.group_id === group.group_id
    ).length;
    return count === MAX_PLAYERS_GROUP
  })
});

const hasUnsavedChanges = ref(false);

/**
 * Добавление игрока в группу
 * @param {number} id - ID игрока 
 */
const handlePlayerToGroup = (id) => {
  if(!hasUnsavedChanges.value){
    hasUnsavedChanges.value = true;
  }
  for (const group of groups){
    const playersInGroup = playersGroups.value.filter(
      p => p.group_id === group.group_id
    ).length

    if(playersInGroup < MAX_PLAYERS_GROUP){
      playersGroups.value.push({
        player_id: id,
        group_id: group.group_id
      });

      return
    }
  }
};

/**
 * Удаление игрока из группы
 * @param {number} playerId - ID игрока
 * @param {number} groupId - ID группы
 */
const handleRemoveFromGroup = (playerId, groupId) => {
  playersGroups.value = playersGroups.value.filter(p =>
    !(p.player_id === playerId && p.group_id === groupId)
  );

  if(playersGroups.value.length === 0){
    hasUnsavedChanges.value = false;
  }
};

/** Вывод в консоль подготовленных данных для сохранения на сервер */
const handleSaveGroups = () => {
  const data = JSON.parse(JSON.stringify(playersGroups.value));
  console.log(data);
  hasUnsavedChanges.value = false;
  alert('Сохранено');
};

onBeforeRouteLeave((to, from, next) => {
  if(!hasUnsavedChanges.value){
    return next()
  }

  if(confirm('Вы не сохранили изменения в составе групп. Сохранить?')){
    if(!allGroupsFull.value){
      alert('Все группы должны быть заполнены')
      return next(false)
    } else {
      handleSaveGroups();
      return next()
    }
  }
  else {
    return next()
  }
})

</script>

<template>
  <main class="w-2/3 m-auto mb-5">
    <div class="flex flex-col items-center mt-10 gap-5">
      <div class="flex justify-between w-full">
        <p class="text-2xl font-bold text-main">Список игроков</p>
        <Button content="Сохранить" :isDisabled="!allGroupsFull" @click="handleSaveGroups" />
      </div>
      
      <Table :data="noGroupPlayers" @doubleСlick="handlePlayerToGroup"/>
      <div class="flex flex-wrap gap-4 justify-center">
        <Group 
          v-for="(item, index) in groups"
          :number = "index + 1"
          :groupId="item.group_id"
          :key="item.group_id"
          :players="groupPlayers[item.group_id] || []"
          @delete="handleRemoveFromGroup"
        />
      </div>
    </div>
  </main>
</template>

