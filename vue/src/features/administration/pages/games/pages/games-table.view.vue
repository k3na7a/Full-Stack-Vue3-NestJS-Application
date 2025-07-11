<script setup lang="ts">
import moment from 'moment'
import TablePaginatedComponent from '@/shared/components/table/paginated-table.component.vue'
import ActionsComponent from '@/shared/components/dropdown/table-actions.dropdown.component.vue'
import ContentLayout from '@/features/administration/layouts/content.layout.vue'
import { useGamesTable } from '../composables/game-table.composable'

const { t, options, loading, tableColumns, response, sort, createGame, updateGame, removeGame, getPaginatedData } =
  useGamesTable()

await getPaginatedData(options.value)
</script>

<template>
  <ContentLayout
    title="administration.games-and-software.games.title"
    subtitle="administration.games-and-software.games.subtitle"
  >
    <template #table>
      <TablePaginatedComponent
        v-model:options="options"
        :loading
        :columns="tableColumns"
        :rows="response.data"
        :pages="response.meta?.pageCount"
        :sort-options="sort"
        :caption="
          t(
            'administration.games-and-software.games.caption',
            {
              start: (response.meta.page - 1) * response.meta.take + 1,
              end: (response.meta.page - 1) * response.meta.take + response.data.length
            },
            response.meta.itemCount
          )
        "
      >
        <template v-slot>
          <div class="d-flex gap-2">
            <ActionsComponent
              size="lg"
              :payload="[
                {
                  title: 'actions.create',
                  icon: ['fas', 'plus'],
                  callback: createGame,
                  theme: 'success'
                }
              ]"
            />
          </div>
        </template>
        <template #user="{ row }">
          <div class="d-flex flex-row align-items-center gap-2">
            <img class="cover-icon" :src="row.cover" />
            <div class="d-flex flex-column">
              <small class="text-muted fst-italic">
                {{ row.id }}
              </small>
              <p class="fw-semibold text-light">
                {{ row.name }}
              </p>
            </div>
          </div>
        </template>
        <template #platforms="{ row }">
          <small class="fw-semibold text-muted">
            <template v-if="row.platforms.length">
              <div class="d-flex flex-column gap-1">
                <small v-for="platform of row.platforms" class="text-primary fw-semibold">{{ platform.name }}</small>
              </div>
            </template>
            <template v-else>-</template>
          </small>
        </template>
        <template #release="{ row }">
          <small class="fw-semibold text-muted">
            {{ moment(row.release_date).format('L') }}
          </small>
        </template>
        <template #actions="{ row }">
          <ActionsComponent
            :payload="[
              {
                title: 'actions.update',
                icon: ['fas', 'pen-to-square'],
                callback: () => updateGame(row),
                theme: 'warning'
              },
              {
                title: 'actions.delete',
                icon: ['fas', 'trash-can'],
                callback: () => removeGame(row),
                theme: 'danger'
              }
            ]"
          >
            <small class="text-primary fst-italic text-nowrap">
              {{ $t('administration.games-and-software.games.item') }}:
              <span class="text-light-alt">{{ row.id }}</span>
            </small>
          </ActionsComponent>
        </template>
      </TablePaginatedComponent>
    </template>
  </ContentLayout>
</template>
