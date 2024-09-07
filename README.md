
# Project: Task 2

## Key Improvements

### Typed Props:
Props are now strongly typed with `ThemeType` and `boolean`, ensuring type safety for `value` and `dense`.

### Typed Emits:
The `emit` function is typed to emit only the `selected` event with the appropriate `ThemeType`.

### Theme Object:
The `themes` object is typed with `as const` to maintain immutability and ensure that the types of its values are locked.

### Error Handling:
The `updateTheme` function includes robust error handling, checking for proper API response format and handling cases where the API or translation function may not exist.

### API and Translations Injection:
The API and translation services are injected using `inject` with appropriate types. The code ensures these services are present before using them.
# Zadanie-2
# Zadanie-2-Vue
