<template>
  <q-select
      v-model="currentTheme"
      :options="themes"
      :dense="props.dense"
      emit-value
      map-options
      @update:model-value="updateTheme"
  >
    <template #selected-item="scope">
      <font-awesome-icon :icon="scope.opt.icon" />
    </template>
    <template #option="scope">
      <q-item v-bind="scope.itemProps">
        <q-item-section side>
          <font-awesome-icon :icon="scope.opt.icon" />
        </q-item-section>
        <q-item-section>
          <q-item-label>
            {{ scope.opt.label }}
          </q-item-label>
        </q-item-section>
      </q-item>
    </template>
  </q-select>
</template>
<script setup lang="ts">
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { alertType } from 'src/enums/alert'; // Enum for alert types
import { theme } from 'src/enums/theme'; // Enum for available themes
import { showAlert } from 'src/hooks/showAlert'; // Alert handling hook
import { usePageTheme } from 'src/hooks/usePageTheme'; // Page theme hook
import { useStorage } from 'src/hooks/useStorage'; // Local storage hook
import { inject, ref } from 'vue';

// Type declaration for ThemeType
type ThemeType = keyof typeof theme;

// Props interface to ensure correct typing of props
interface Props {
  value: ThemeType | null; // Nullable theme type for selected value
  dense: boolean; // Boolean to define UI density
}

// Define the props and emits types
const props = defineProps<Props>();
const emit = defineEmits<{
  (event: 'selected', theme: ThemeType): void;
}>();

// Inject the API service and translation function, with types defined
const api = inject<{ post: (url: string, data: any) => Promise<any> }>('api');
const trans = inject<(key: string) => string>('trans');

const storage = useStorage();
const pageTheme = usePageTheme();

// Set the current theme, defaulting to the page theme if no value is passed in
const currentTheme = ref<ThemeType>(props.value || pageTheme.get());

// Predefined themes with corresponding icons
const themes = [
  { label: 'Motyw jasny', value: theme.LIGHT, icon: 'fa-regular fa-sun' },
  { label: 'Motyw ciemny', value: theme.DARK, icon: 'fa-regular fa-moon' },
  { label: 'Motyw dostępności (WCAG 2.1 AA)', value: theme.WCAG, icon: 'fa-regular fa-circle-half-stroke' },
] as const;

// Function to update the selected theme
const updateTheme = async (value: ThemeType) => {
  try {
    pageTheme.set(value); // Update the page theme

    // Safeguard with optional chaining, ensure the API is defined
    const response = await api!.post('/user-settings', { theme: value });

    // Check the response format and display success or throw error
    if (response?.data && response.data.success && response.data.data) {
      showAlert(alertType.SUCCESS, trans?.('Poprawnie zapisano ustawienia') || 'Settings saved successfully');
      storage.setItem('theme', response.data.data.theme); // Save the theme in local storage
      emit('selected', response.data.data.theme); // Emit the selected event with the new theme
    } else {
      throw new Error('Invalid response format');
    }
  } catch (error) {
    // Display error message if something goes wrong during the API call
    showAlert(alertType.ERROR, trans?.('Nie udało się zapisać ustawień') || 'Failed to save settings');
    console.error('Error updating theme:', error);
  }
};
</script>
