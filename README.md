# Two-Factor Authentication Component

A modern, animated two-factor authentication component built with Svelte and Motion One. This component provides a delightful user experience with smooth animations and visual feedback.

## Features

### 1. Input Handling

- Six-digit code input with auto-focus navigation
- Numeric input validation
- Automatic focus advancement
- Backspace navigation support
- Arrow key navigation support
- Paste functionality for the entire code

### 2. Visual States

- **Initial State**: Displays a locked icon with digit counter
- **Complete State**: Shows an unlocked icon when all digits are entered
- **Success State**: Displays a success icon with celebratory animation
- **Error State**: Shows error feedback with shake animation

### 3. Animations

Using Motion One for smooth, performant animations:

#### Success Animations

- Icon scales and rotates with spring physics
- Background circle pulses with a fade effect
- "Unlocked!" text slides up with spring animation

#### Error Animations

- Shake animation on input fields
- Red color transition
- Error message slide-in

#### General Animations

- Smooth icon transitions
- Button state transitions
- Text slide-in animations

### 4. Accessibility

- Numeric input mode for mobile keyboards
- Focus management
- Clear visual feedback
- Keyboard navigation support

## Implementation Details

### State Management

```typescript
let inputs = Array(6).fill("");
let inputRefs = Array(6);
let isUnlocked = false;
let isError = false;
```

### Animation System

Using Motion One for advanced animations:

```typescript
// Success Animation
const successAnimation = (node) => {
  animate(
    node,
    {
      scale: [1, 1.2, 1],
      rotate: [0, -10, 10, -10, 0],
    },
    {
      duration: 0.6,
      easing: spring({ stiffness: 200, damping: 10 }),
    }
  );
};

// Error Animation
const shakeAnimation = (node) => {
  animate(
    node,
    { x: [0, -4, 4, -4, 4, 0] },
    { duration: 0.5, easing: "ease-in-out" }
  );
};
```

### Input Handling

- Auto-focus next input on entry
- Backspace navigation to previous input
- Arrow key navigation between inputs
- Paste handling for full code

```typescript
const handleInput = (index, event) => {
  // Numeric validation
  if (!/^\d*$/.test(value)) return;

  // Auto-advance focus
  if (value && index < 5) {
    inputRefs[index + 1].focus();
  }
};
```

### Validation & Submission

- Real-time validation of entered digits
- Auto-submit when code is complete
- Error handling with visual feedback
- Success celebration animations

```typescript
const handleSubmit = () => {
  const code = inputs.join("");
  if (code === "123456") {
    isUnlocked = true;
    // Trigger success animations
  } else {
    isError = true;
    // Trigger error animations
  }
};
```

## Styling

The component uses Tailwind CSS for styling with:

- Responsive design
- Smooth transitions
- Focus states
- Error states
- Success states

```css
:global(.auth-input) {
  @apply w-12 h-16 text-3xl text-center border-2 border-gray-200 rounded-lg 
         focus:border-blue-500 focus:ring-2 focus:ring-blue-200 focus:outline-none
         transition-all duration-200 bg-white relative;
}
```

## Dependencies

- Svelte
- Motion One (for animations)
- Tailwind CSS (for styling)

## Usage

1. Install dependencies:

```bash
npm install
```

2. Import the component:

```svelte
import TwoFactorAuth from './lib/TwoFactorAuth.svelte';
```

3. Use in your Svelte app:

```svelte
<TwoFactorAuth />
```

## Development

Run the development server:

```bash
npm run dev
```

## Building

Build for production:

```bash
npm run build
```
