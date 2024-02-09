- options to develop react native app:
  - expo snack => web client to develop react native
  - expo cli
    - disadvantages:
        - inability to write native code and using library that use native code.
  - react-native-cli
    - advantages 
        - ability to run native code



- in emulator: 
  - press ctrl + m to open the menu.
  - press ctrl + d to open the debug menu.
  - double r to reload the app.

# Styling

- style prop: 
  - style prop is a common properties in component we can set the value inline, or by defining StyleSheet. the naming of the properties is like css but with camel case
  
- StyleSheet:
  - one of its benefits is that it validate the style properties and warn you if you use a wrong property. the inline style doesn't do that.
  - we can pass multiple styles from the stylesheet by stetting the style prop to an array and if any property repeated the last dominate.

---

# Main Component:
## View
- used as a single container to group other components inside it. 
- it's not necessary to group others and can be use a self closing one to just display some element on the screen.
- SafeAreaView: 
  - it's a view that take care of the notch and the status bar; by adding padding to the top of the screen. work only on ios.
  - to use it you can add:
    - paddingTop: Platform.OS === 'android' ? StatusBar.currentHeight : 0,
## Text
- render text on the screen.
## ScrollView 
- you can use it like View but it support scrolling.
## Image
- if it's yours, you can import it and use it as value for a source prop; if not and it come from the uri you pass set the source prop as object {{ uri: "", }}.
- resizeMode= {cover, contain..}
- in local image you don't need to set the width and height because it can be read from the image metadata, but in remote image you need to set the width and height.
## TextInput (self closing)

## Touchable Components in React Native

React Native provides a set of **Touchable** components that allow you to capture touch events and create interactive user interfaces. These components enhance the user experience by providing feedback when touched. Here are some important Touchable components:

### 1. TouchableOpacity

- **Description:** `TouchableOpacity` is a basic touchable component that reduces the opacity of the wrapped content when touched.
  
- **Use Case:** Ideal for creating buttons or elements that should appear visually responsive to touch.

- **Example:**
  ```jsx
  import { TouchableOpacity, Text } from 'react-native';

  const MyButton = () => (
    <TouchableOpacity onPress={() => console.log('Button Pressed')}>
      <Text>Press Me</Text>
    </TouchableOpacity>
  );
  ```

### 2. TouchableHighlight

- **Description:** `TouchableHighlight` underlays the wrapped content with a color when touched and provides customizable feedback.
  
- **Use Case:** Suitable for buttons or components where highlighting is desired on touch.

- **Example:**
  ```jsx
  import { TouchableHighlight, Text } from 'react-native';

  const MyButton = () => (
    <TouchableHighlight onPress={() => console.log('Button Pressed')} underlayColor="lightgray">
      <Text>Press Me</Text>
    </TouchableHighlight>
  );
  ```

### 3. TouchableWithoutFeedback

- **Description:** `TouchableWithoutFeedback` doesn't provide any visual feedback on touch; it's used for capturing touch events without changing the appearance of the component.

- **Use Case:** Useful for advanced touch handling where visual feedback is custom-designed.

- **Example:**
  ```jsx
  import { TouchableWithoutFeedback, View } from 'react-native';

  const MyTouchableComponent = () => (
    <TouchableWithoutFeedback onPress={() => console.log('Touched')}>
      <View>
        {/* Content here */}
      </View>
    </TouchableWithoutFeedback>
  );
  ```

### 4. TouchableNativeFeedback (Android only)

- **Description:** `TouchableNativeFeedback` provides the native Android ripple effect on touch.

- **Use Case:** Android-specific touchable component with a native feel.

- **Example:**
  ```jsx
  import { TouchableNativeFeedback, View } from 'react-native';

  const MyButton = () => (
    <TouchableNativeFeedback onPress={() => console.log('Button Pressed')}>
      <View>
        {/* Content here */}
      </View>
    </TouchableNativeFeedback>
  );
  ```

### 5. Pressable (Introduced in React Native 0.64)

- **Description:** `Pressable` is a cross-platform component that can be used for handling various touch interactions with customizable feedback.

- **Use Case:** Versatile touchable component suitable for a wide range of touch interactions.

- **Example:**
  ```jsx
  import { Pressable, Text } from 'react-native';

  const MyComponent = () => (
    <Pressable onPress={() => console.log('Pressed')}>
      {({ pressed }) => (
        <Text style={{ opacity: pressed ? 0.5 : 1 }}>
          Press Me
        </Text>
      )}
    </Pressable>
  );
  ```
---



---

# Alert API in React Native

React Native provides an `Alert` API that allows developers to display native alert dialogs on both iOS and Android platforms. The `Alert` API is commonly used for presenting simple messages, prompts, or confirmations to users.

## Basic Usage

To use the `Alert` API, you can call the `Alert.alert` method and pass the desired title and message as arguments. For example:

```jsx
import { Alert } from 'react-native';

// ...

Alert.alert('Alert Title', 'This is an alert message.');
```

## Handling User Actions

The `Alert.alert` method allows you to handle user actions such as pressing the OK or Cancel button. You can provide an array of buttons with specific callbacks:

```jsx
import { Alert } from 'react-native';

// ...

Alert.alert(
  'Confirmation',
  'Do you want to proceed?',
  [
    { text: 'Cancel', style: 'cancel', onPress: () => console.log('Cancel Pressed') },
    { text: 'OK', onPress: () => console.log('OK Pressed') },
  ]
);
```

## Prompt Input

For input prompts, you can use the `Alert.prompt` method:

```jsx
import { Alert } from 'react-native';

// ...

Alert.prompt(
  'Enter Text',
  'Please enter some text:',
  (text) => console.log('You entered: ' + text),
  'plain-text',
  'Default Value'
);
```

## Styling

While the appearance of the alert dialog is platform-specific, you can customize the styling of buttons using the `style` property. For example:

```jsx
import { Alert } from 'react-native';

// ...

Alert.alert(
  'Stylish Alert',
  'This alert has stylish buttons.',
  [
    { text: 'Cancel', style: 'cancel', onPress: () => console.log('Cancel Pressed') },
    { text: 'OK', style: 'destructive', onPress: () => console.log('OK Pressed') },
  ]
);
```

## Important Considerations

- **Blocking Nature:** The `Alert` component is blocking, meaning that the code execution is paused until the user interacts with the alert.

- **Platform Consistency:** While the API abstracts platform-specific implementations, developers should be aware of platform-specific guidelines and behaviors.

- **Async Calls:** If you need non-blocking behavior, consider using alternative libraries or implementing a custom modal component.

The `Alert` API is a convenient way to display simple dialogs in React Native applications, providing a consistent user experience across different platforms.

--- 
