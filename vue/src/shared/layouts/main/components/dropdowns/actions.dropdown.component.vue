<script setup lang="ts">
import { useI18n } from 'vue-i18n'
import { useRouter } from 'vue-router'

import { UserDto } from '@/library/dto/user.dto'

import DropdownComponent from '@/shared/components/dropdown/base/dropdown.component.vue'
import { user_actions } from '@/shared/layouts/main/config/user-actions.config'

const { t } = useI18n()
const $router = useRouter()

const { authenticatedUser, signout } = defineProps<{
  authenticatedUser: UserDto
  signout: () => void
}>()

const USER_ACTIONS = user_actions($router, signout)
</script>

<template>
  <DropdownComponent v-if="authenticatedUser" dropdown-align="end">
    <template #button>
      <font-awesome-icon :icon="['fas', 'user']" />
    </template>
    <template #menu="{ close }">
      <div class="d-flex flex-column gap-1">
        <div class="px-2 py-1">
          <div class="d-flex align-items-center gap-2">
            <img class="avatar-icon rounded-circle" :src="authenticatedUser.profile.avatar" />

            <div class="d-flex flex-column flex-grow-1">
              <p class="text-light fw-semibold">{{ authenticatedUser?.getFullName() }}</p>
              <small class="text-light-alt d-block text-truncate">{{ authenticatedUser?.email }}</small>
            </div>
          </div>
        </div>
        
        <div class="d-flex flex-column gap-2">
          <template v-for="(section, index) of USER_ACTIONS" :key="index">
            <hr v-if="index" class="dropdown-divider mx-1 my-0 bg-secondary opacity-50" />
            <div class="d-flex flex-column gap-1">
              <template v-for="action of section.children" :key="action.title">
                <button
                  class="dropdown-item d-flex justify-content-between align-items-center px-2 m-0"
                  type="button"
                  :disabled="action.disabled"
                  @click="(event: MouseEvent) => {
                    action.callback(event, t)
                    close()
                }"
                >
                  <span class="text-truncate pe-2">{{ $t(action.title) }}</span>
                  <span style="width: 1.5rem" class="text-center"><font-awesome-icon :icon="action.icon" /></span>
                </button>
              </template>
            </div>
          </template>
        </div>
      </div>
    </template>
  </DropdownComponent>
</template>
