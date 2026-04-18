<script setup>
const {number, players, groupId} = defineProps(['number', 'players', 'groupId'])

const emit = defineEmits(["delete"]);

const handleDeletePlayer = (playerId) => {
   emit('delete', playerId, groupId)
}
</script>

<template>
   <div class="flex flex-col gap-2">
        <p class="self-center text-lg font-medium text-main">Группа {{ number }}</p>
         <ul class="bg-slate-200 h-40 w-60 p-3">
            <li class="cursor-pointer hover:bg-main hover:text-white"
               v-for="(player) in players" 
               :key="player.player_id"
               @dblclick="handleDeletePlayer(player.player_id)"
            >
               {{ player.fio }}
            </li>
         </ul>
   </div> 
</template>