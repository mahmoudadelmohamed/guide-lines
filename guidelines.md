## Technology Stack
### General
- Typescript
- Gitlab
- Axios
- NPM
- Eslint
### Mobile Apps
- React Native
## Development Guidelines
### Mobile Apps
- Prefer functional components over class components.
  - To replace imperative patterns using refs: create a separate context and context provider component that provides all utilities and states that a component may need and create a hook that can be used to alter a component's state imperatively.
- Prefer hooks over HOCs. Use HOCs if and only if there is a need to alter multiple components' structure.
- Use the components guide [here](#components-guide).
- Use the React component folder structure [here](#react-components-folder-structure).
- Don't add static text values directly inside Typgoraphies, instead use the static text translation generation tool that adds these key value pairs in the backend. [reference]
- Prefer named exports over default exports.
- Always enable Eslint and follow its rules.
- Mobx backend stores represent our data and service layers. Responsible for fetching data, holding this data and persisting this data. No data manipulation should be done inside Mobx. Data manipulation should be done by the data consumers and not providers.
- Mobx UI stores are used for holding global persistable app states.
- Refrain from having states/observables that rely on other states/observables. Instead, use Mobx computed values or functions that compute the desired state based on the others. This way we have only one source of truth and all other states are derived from them. For example to check if a user is logged in:
  - Don't: create an observable called isLoggedIn that is set to true after the login method succeeds.
  - Do: create a computed value that returns true if a user has an access token and false otherwise.
- Prefer flex over absolute positioning. Use absolute positioning if and only if you need to stack elements over each other.
- Prefer the usage of if statements over ternary operators when returning JSX.
- Always cast conditions to boolean when conditionally showing or hiding JSX. Example: ```!!someString && (...)```
- Prefer conditional JSX over ternary operator JSX. Example:
  - Don't ```someBoolean ? () : ()```
  - Do ```someBoolean && ()``` ```!someBoolean && ()```
- In Typescript prefer TypeGuards over type casting. See reference [here](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates)
- All file/directory names should be in kebab-case to prevent any OS case sensitivity issues.
- Never map props/observables to states. Map them if and only if these values will be used as initial values for these states.
- Don't define anonymous functions inside functional components: either extract these functions outside the component or wrap these functions with useCallback or useMemo.
  - Ex: 
  ```
  const executedFn = useCallback(() => null, []);
  <TouchableOpacity onPress={executedFn}
  ```
  OR
  ```
  const executedFn = () => null;
  const Component = (props) => (
      <TouchableOpacity onPress={executedFn} />
  );
  ```

- Constant values that live in the global scope should be in UPPER_CASE
- Class names, Types, Interfaces and React components should be all in PascalCase
- All other variables should be in camelCase
- Avoid magic numbers
  - Do
  ```
  const payButtonActiveOpacity = 0.9;
  <TouchableOpacity activeOpacity={payButtonActiveOpacity} />
  ```

  - Don't
  ```
  <TouchableOpacity activeOpacity={0.9} />
  ```

- Avoid all inline styles
  - Do:
  ```
  <TouchableOpacity style={selectStyle('className')} />
  ```

  - Don't:
  ```
  <TouchableOpacity style={{margin: 8}} />
  ```


#### React Components folder structure
All React components (screens, buttons, layout, ...) share the same structure. Each component has:
- A separate directory where it lives.
- An index.tsx file where the React Component definition lives.
- A styles.ts file where there is an exported function that takes as an argument the theme and returns the styles.
- A utils.ts file where all utility functions live.
- A types.ts file where all Typescript type definitions live for example Props.
You can use this script [component-generator.sh](/bin/component-generator.sh), to easily generate a component or a screen with this structure.

##### Theming
- Palette
  - Palette main theme colors, these are colors that should exist in any project and are provided by the design team before the start of the project. Each color has many values which are:
    - value: The main color value
    - contrast: The color that best contrasts with the main color frequently used for placing text on the main value
    - disabledValue: The disabled version of the main color
    - disabledContrast: The contrasting value to the disabled color
  - The palette can contain other custom colors other than the main theme colors and these live inside the others property inside the palette
     - Do:
     ```
     color: primary.value
     ```
     - Don't:
     ```
     color: #FDD72F
     ```
- Typography
    - Typography is a group of styled text variants such as h1, subtitle1,..
      - Ex:
      ```
      <Typography variant="subtitle1">TEXT_HERE</Typography>
      ```

    - Each typography variant holds its font, font size, font weight, line height, letter spacing.
    - There are main typography variants such as h1-h6, subtitle1-2, ... And there are custom variants that you can define inside the others property.
    - To create a variatn you can use the class TypographyVariant and call it with the root font size as the first parameter and all following dimensions will be in "em" relative to that font size.
- Spacing
    - Spacing is the module that is responsible for all margins and paddings.
    - They all should be multiples of a base unit which is usually 8px.
    - Any margins and/or paddings should be using the spacing(n) function and not given a direct value.
        - Do:
        ```
        margin: spacing(1)
        ```
        - Don't:
        ```
        margin: 8
        ```
- ThemeProvider: Provides the theme to all components inside koinz-rn-ui. It calculates the theme's value depending on its dependencies that you provide to the component.
- It's very critical that when we're using typography variants or palette colors that we don't override these colors by custom styles as this breaks the loop and will need refactoring if any color from the theme has changed.
- Useful hooks:
    - useTheme: A hook that returns the current value of the theme
    - selectStyle: A hook that takes the styles and updates them whenever the theme changes. Returning a function that chooses styles by style name.


#### Components guide
- We have two types of components in a functionality perspective:
  - Dumb: Components that do not take any actions on their own and all actions are passed as props.
  - Smart: Components that can take actions, fetch data, manipulate data...
- We have three types of components in a reusability perspective:
  - Universal: Components that are customizable in UI and functionality. Their main purpose is to be used in multiple projects. These components should live in an external library. These components should be totally Dumb. Example: Typography.
  - Global: Components thar are customizable in functionality but not necessarily in UI. Their main purpose is to be used everywhere inside a specific project. These components should live inside the project in a components folder. These components can be partially smart. Example: Success Screen.
  - Local: Components that are not customizable and mainly require no props. Their main purpose is to divide code into chunks for better readability, maintainability and ease of generalization. These components should live inside the directory where they are being used. These components should be totally Smart. Example: Screens (pages), Screen sections.
