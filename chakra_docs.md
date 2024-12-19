# Combined Source Files
Generated on: Thu Dec 19 12:08:47 PST 2024
Source directory: /home/donald/src/ext/chakra-ui/apps/www/content
File pattern: *.mdx


## /home/donald/src/ext/chakra-ui/apps/www/content/blog/00-announcing-v3.mdx

```text
---
title: Announcing v3
description: Today, we're excited to announce the release of Chakra UI v3.0
type: release
version: "3.0"
authors: ["sage"]
publishedAt: "2024-10-22"
---

Today, we're excited to announce the long-awaited release of Chakra UI v3. The
feedback for Chakra v3 has been incredible and we appreciate those who took the
time to test and catch bugs.

Chakra v3 is a complete rewrite of Chakra to enhance it's performance, speed and
consistency across components. We've also added over 25 new components, and
that's just the beginning.

## Credits

Before giving you a quick overview, I'd like to start by acknowledging those
whose ideas and efforts contributed to the redesign of Chakra v3.

- **[Park UI](https://park-ui.com/)**: The groundwork by Christian and Phil laid
  the foundation of the design philiosophy in Chakra v3. Consistent sizing,
  colors, shadows, and naming convention is the heart and soul of a design
  system.

- **[Panda](https://panda-css.com/)**: The theming and styling APIs in Panda CSS
  created paved the way for Chakra v3's styling engine. The end goal was to make
  it almost effortless to switch between Park UI and Chakra UI.

- **[Radix Themes](https://www.radix-ui.com/themes)**: For inspiring the
  playground, composable animations and accent color system.

- **[Shadcn](https://ui.shadcn.com/)**: For inspiring the CLI and driving the
  idea of copy-paste snippets which Chakra now embraces.

We also want to appreciate these individuals have contributed consistently and
helped to shape v3.

[Christian Schröter](https://x.com/grizzly_codes),
[Esther Adebayo](https://x.com/_estheradebayo),
[Eelco](https://x.com/pagebakers), [Alex Stahmer](https://x.com/astahmer_dev),
[Tolulope Oyewumi](https://x.com/Delighteebrands),
[Abraham Anuoluwapo](https://x.com/anubra266),
[Ivica Batinić](https://x.com/_isBatak)

## Design Architecture

In Chakra v3, we're unifying our ecosystem of tools by combining the headless
library, Ark UI with the styling APIs in Panda CSS, then using Park UI as the
design system.

<Image
  src="/images/annoucement-image-frameworks.png"
  alt="Chakra v3 ecosystem"
  objectFit="contain"
  fill
  height="400px"
/>

We've redesigned most of the components from ground-up to ensure they are all
consistent and use design tokens in most cases.

## Semantic Tokens

Semantic tokens make it easy to personalize your token without having to restyle
every component by hand. Chakra v3 provides 7 semantic tokens for each color
palette, giving you ultimate flexibility without having to think about dark
mode.

- **solid:** The solid color of the palette

- **muted:** A muted version of the palette

- **subtle:** A subtle version of the palette, lower then the muted one.

- **emphasized:** A more pronounced version of the palette

- **contrast:** A color that goes on the solid background (also called
  "on-solid")

- **fg:** The foreground color of the palette

- **focusRing:** The focus ring color of the palette

<br />
Here's an example of using the red color in a semantic way that automatically
adapts to dark mode.

```tsx /red.subtle/ /red.fg/ /red.solid/ /red.contrast/
// A subtle version of red
<Box bg="red.subtle" color="red.fg">
  Welcome
</Box>

// A solid version of red
<Box bg="red.solid" color="red.contrast">
  Welcome
</Box>
```

To take this to the next level, you can leverage the new `colorPalette` feature.
It allows you create a color placeholder that be swapped to any color at any
depth on the DOM tree using CSS variables.

```tsx /colorPalette="red"/
<Box colorPalette="red">
  <Box bg="colorPalette.subtle" color="colorPalette.fg">
    Welcome
  </Box>
  <Box bg="colorPalette.solid" color="colorPalette.contrast">
    Welcome
  </Box>
</Box>
```

## Open by default

We moved away from closed components to open, compound components by default.
This makes it easier for you compose your own components and reduce the
maintenance on our end.

To illustrate the difference, here's how you'd create a checkbox in v2.

```tsx
<Checkbox>Click me</Checkbox>
```

Here's a contrived example of the checkbox component in Chakra v2.

```tsx
export const Checkbox = forwardRef(function Checkbox(props, ref) {
  const { children, iconColor, iconSize, icon, inputProps, ...checkboxProps } =
    props

  const checkbox = useCheckbox(checkboxProps)

  return (
    <chakra.label {...checkbox.getRootProps()}>
      <input {...checkbox.getInputProps(inputProps, ref)} />
      <chakra.span {...getCheckboxProps()}>
        <CheckIcon as={icon} color={iconColor} size={iconSize} />
      </chakra.span>
      {children && (
        <chakra.span {...checkbox.getLabelProps()}>{children}</chakra.span>
      )}
    </chakra.label>
  )
})
```

While the snippet above looks easy to use, you get to the point where
customization becomes a challenge. Questions like these often arise:

- how to pass props to the underlying icon?
- how to pass props to the label that renders the children?
- how to change the underlying animation of the checkbox?

This often leads to a component with many props that can be confusing to learn.

We've now made Chakra UI composable by default, this means you no longer get
closed components by default.

Here's how you'd create a checkbox with the new approach.

```tsx
<Checkbox.Root>
  <Checkbox.HiddenInput />
  <Checkbox.Control>
    <Checkbox.Indicator />
  </Checkbox.Control>
  <Checkbox.Label>Click me</Checkbox.Label>
</Checkbox.Root>
```

It's a lot more code and can be overwhelming to write. To account for this
slight tweak in DX, we've created the concept of "snippets" which can help you
wrap the composable checkbox and get you back to the initial v2 style.

Let me explain how it works:

## Snippets

By running the snippets CLI command, Chakra composes the components in Chakra
and puts it in your project. Giving you maximum control of every aspect.

```sh
npx @chakra-ui/cli@init snippets add
```

After running this you should see primitive components add to the
`components/ui/*` directory in your project.

```tsx
// components/ui/checkbox.tsx
export const Checkbox = forwardRef(function Checkbox(props, ref) {
  const { children, icon, inputProps, ...restProps } = props
  return (
    <Checkbox.Root {...restProps}>
      <Checkbox.HiddenInput {...inputProps} ref={ref} />
      <Checkbox.Control>
        <Checkbox.Indicator>{icon}</Checkbox.Indicator>
      </Checkbox.Control>
      {children && <Checkbox.Label>{children}</Checkbox.Label>}
    </Checkbox.Root>
  )
})
```

This makes it easy to achieve the same DX as v2 in v3.

```tsx
import { Checkbox } from "@/components/ui/checkbox"

const Demo = () => <Checkbox>Click me</Checkbox>
```

## Improve runtime performance

- **Remove runtime functions in theme:** Previously we allowed the use of
  functions in the theming system which could negatively impact runtime
  performance. We've now switched to a variant-based system called "recipes",
  largely inspired by Panda CSS.

- **Externalize styling engine:** The styling engine is initialized outside of
  the React tree and consumed by the components, providing a faster style
  resolution and rendering times in every component.

- **Migration to CSS animations:** We removed the `framer-motion` dependency in
  favor of using the platform's animation features. This gives us a performance
  boost and reduces the bundle size of your application.

## New components

We've added new components from Ark UI. These headless components are powered by
statecharts and work consistently across major frameworks. We truly believe the
future of UI libraries is framework-agnostic.

<Image
  src="/images/annoucement-new-components.png"
  alt="Chakra v3 ecosystem"
  objectFit="contain"
  fill
  height="400px"
/>

We also added new presentational components to save you time when building UIs.

## Embracing the ecosystem

We don't want to re-invent the wheel for common needs. In the spirit of this, we
removed a good number of modules from Chakra to keep us focused on delivering
best-in-class components.

- removed our icons in favor of a more robust icon library like `lucide-react`
  or `react-icons`
- removed our internal color mode management in favor of `next-themes` or
  `remix-themes`
- removed most hooks in favor of robust hook libraries like `react-use` or
  `use-hooks-ts`

## What's next

- Picker components from Ark: ColorPicker, DatePicker
- Redesigned Chakra UI Pro
- Redesigned Figma UI Kit
- Redesigned FigPilot: Our code generation tool for Figma that converts any
  design to Chakra compatible code.
- Explore the
  [React 19 style tag](https://react.dev/reference/react-dom/components/style)

## FAQs

### Does Chakra v3 use Panda internally?

No. To reduce the breaking change surface, we've decided to keep emotion (and
runtime css-in-js) to preserve the dynamic styling benefits. There's already a
lot of changes in v3, so we'll handle this progressively.

We might not even have to use Panda at all. The progress of the
[style tag](https://react.dev/reference/react-dom/components/style) in React 19
is very promising and we give Chakra even more performance boost without any
migration cost.

### Are we going to have all components from Ark UI?

Yes, Ark UI features a lot of useful components. We've included some of them
already in Chakra, but we'll bring in more interesting components like
`ColorPicker`, `DatePicker` over time.

### Do we really need to use the snippets?

No, you don't have to use the snippets if you're not a fan. We recommend doing
this though, it helps simplify your development experience since you'll anyway
have to do the same work on your end.

If you find the number of snippets overwhelming, remove the snippets you don't
need.

### Why the new logo?

The launch of v3 marks a new era for Chakra UI. The new era synchronizes Zag.js,
Ark UI and Panda CSS in a very unique way. This alone warrants a new story and
new brand.

### Why did it take so long to launch v3?

I ask myself the same question as well. It's an Herculean task to design the
foundations of Chakra UI in a way that is framework agnostic and entirely built
on statecharts. This hadn't been done before and took a lot of time to get that
stable. In the end, it was worth the effort and we appreciate your patience.

## Get started

We invite you to try Chakra v3 today and experience the delight of building user
interface with speed. **Get started with Chakra UI v3 now**

```sh
npm i @chakra-ui/react @emotion/react
```

Explore the [migration guide](/docs/get-started/migration) to upgrade your
project to Chakra UI v3.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/blog/01-chakra-v2-vs-v3-a-detailed-comparison.mdx

```text
---
title: Chakra v2 vs v3 - A Detailed Comparison
description: Discover new features in Chakra UI v3.
type: article
authors: ["esther"]
publishedAt: "2024-10-28"
---

We recently launched Chakra UI version 3, which brings a huge set of changes,
improvements and new features.

With a major update like this, you may wonder, what has changed, what’s new, and
is migrating really worth it?

In this article, we'll take a deep dive into the key differences between version
2 and version 3. The goal is to give you a better understanding of version 3 and
help you decide if its time to migrate your projects.

## Simplified Installation

The installation process is one of the first noticeable differences. Previously,
you had to install multiple packages to get Chakra working in your project:

- `@chakra-ui/react`
- `@emotion/react`
- `@emotion/styled`
- `framer-motion`

But with **v3**, things are much simpler. You only need:

- `@chakra-ui/react`
- `@emotion/react`

This reduces the number of dependencies and results in a quicker setup.

**version 2:**

```bash
npm install @chakra-ui/react @emotion/react @emotion/styled framer-motion
```

**version 3:**

```bash
npm install @chakra-ui/react @emotion/react

```

## New Provider Setup

In previous versions, you had to wrap your application with `ChakraProvider` to
access Chakra’s default theme. However, we've now replaced `ChakraProvider` with
the `Provider` component.

`Provider` composes the following:

- `ChakraProvider` from `@chakra-ui/react` for the styling system
- `ThemeProvider` from `next-themes` for color mode

**version 2:**

```jsx
import { ChakraProvider } from "@chakra-ui/react"

function App() {
  return <ChakraProvider>{/* the rest of your app */}</ChakraProvider>
}
```

**version 3:**

```jsx
import { Provider } from "@/components/ui/provider"

function App() {
  return <Provider>{/* the rest of your app */}</Provider>
}
```

## Component Snippets

A common pain point for developers is the repetitive task of building common UI
components. To solve this, we’ve introduced Component Snippets.

Snippets are pre-built components designed to accelerate your development
process.

With the new [Chakra CLI](/docs/get-started/cli#chakra-snippet) you can
instantly add ready-made components to your project with a single command.

```bash
# Add all snippets
chakra snippet add

# Add a specific snippet
chakra snippet add button

```

Once you run the command, you'll find the components neatly organized in a
`/components/ui` folder inside the **src** directory.

You can then import your desired component snippet directly from the components
folder

```bash
import { Button } from "@/components/ui/button"
```

## New Components

We listened to you, our community, and in this version, we added 25+ new
components to enhance your development workflow.

Some of the new components include:

- File Upload
- Clipboard
- Timeline
- Password Input
- Segmented Control
- Toggle Tip
- Custom Select
- And a lot more

Check out all [components here](/docs/components/concepts/overview). Each new
component is built with the same focus on accessibility and design consistency
that Chakra UI is known for.

## Introduction of State Machines

One of the biggest updates in this version is the use of state machines for
logic-based components. Chakra UI v3 now uses Ark UI as the foundation for these
components.

State machines bring a new level of stability to Chakra UI components,
especially for applications that require complex and interactive user
interfaces.

While this isn’t something you’ll interact with directly as a user, it’s
important to know that using state machines under the hood significantly
improves the performance of components like modals, menus, popovers, and other
complex UI elements.

Now, you can expect more consistent behaviour across Chakra UI components.

## Streamlined Component Imports

In previous versions, you had to import multiple components and component parts
into your project.

For example, you had to manually import both `List` and `ListItem` separately

**version 2:**

```jsx
import { List, ListItem } from "@chakra-ui/react"

function App() {
  return (
    <List>
      <ListItem>Item 1</ListItem>
    </List>
  )
}
```

Version 3 offers a more cohesive API that keeps related components together. We
introduced a more organized approach to component imports, reducing the need for
multiple imports and making your codebase easier to maintain.

**version 3:**

```jsx
import { List } from "@chakra-ui/react"

function App() {
  return (
    <List.Root>
      <List.Item>Item 1</List.Item>
    </List.Root>
  )
}
```

Now, you only need to import `List` and use the object notation to compose the
other components.

This pattern of nesting primary components under a main “Root” container and
related subcomponents streamlines Chakra’s API and keeps your imports cleaner.

## Enhanced Theme Setup

We’ve reimagined the theme setup for better modularity. In previous versions,
themes were typically extended using the `extendTheme` function:

**version 2:**

```jsx
const theme = extendTheme({
  colors: {
    brand: {
      100: "#f7fafc",
      900: "#1a202c",
    },
  },
})
```

In **v3**, the new approach leverages the `createSystem` function, where tokens
are defined under the `theme` key in your system config:

**version 3:**

```jsx
import { createSystem } from "@chakra-ui/react"

export const system = createSystem(defaultConfig, {
  theme: {
    tokens: {
      fonts: {
        heading: { value: `'Figtree', sans-serif` },
        body: { value: `'Figtree', sans-serif` },
      },
    },
  },
})
```

This new setup makes it easier to customize and scale your design system.

## Enhanced Design Tokens

In version 3, we’ve also updated our design tokens. You’ll notice changes in:

- **Colors**: The [color palette](/docs/theming/colors) has been expanded and
  fine-tuned for vibrancy and versatility. This ensures that your designs are
  more visually appealing and accessible.
- **Animations:** We’ve added more support for
  [animations](/docs/theming/animations) using vanilla css, making your app more
  perfomant.
- **Design Tokens**: Improved and more robust [tokens](/docs/theming/tokens) for
  better design consistency.
- **Introduction of Semantic Tokens:** We added built-in
  [semantic tokens](/docs/theming/semantic-tokens) to make it easier to map your
  design system to both light and dark color modes.

## Updated Props

Props have also received a
[major update](/docs/get-started/migration#prop-changes) in this version. A
great example is the `gap` prop, which now replaces the `spacing` prop for
`Stack` and similar components.

**version 2:**

```jsx
<Stack spacing={4}>
  <Box>Item 1</Box>
  <Box>Item 2</Box>
</Stack>
```

**version 3:**

```jsx
<Stack gap={4}>
  <Box>Item 1</Box>
  <Box>Item 2</Box>
</Stack>
```

This change is more in line with modern CSS practices and helps developers
manage spacing more efficiently, especially in flex and grid layouts.

Not only that, in v2, **boolean props** were typically prefixed with `is`, such
as `isDisabled`. In v3, this has been simplified by removing the `is` prefix.

**version 2:**

```jsx
<Button isDisabled>Click Me</Button>
```

**version 3:**

```jsx
<Button disabled>Click Me</Button>
```

This change makes prop names more intuitive and easier to read, aligning with
standard HTML practices.

## We Said Goodbye to `chakra-icons`

In a move towards simplicity, v3 has removed the `chakra-icons` package.
Instead, we encourage developers to use libraries like `lucide-react` or
`react-icons` for their icon needs.

This shift reduces dependency bloat and allows you to tap into a broader range
of icon options available in the React ecosystem.

## Go Faster With Chakra v3

If you’re currently using Chakra UI v2, now is a great time to consider
[migrating to version 3](/docs/get-started/migration).

The new features, such as component snippets, enhanced design tokens, and state
machine-powered components, are designed to streamline your development process
and improve your overall developer experience.

Try out Chakra v3 and let us know what you think.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/blog/02-chakra-3.2.mdx

```text
---
title: Chakra 3.2
description:
  "Introducing new color components, input masking example, and CLI improvements"
type: release
version: "3.2"
authors: ["sage"]
publishedAt: "2024-11-21"
---

We're excited to announce the release of Chakra UI v3.2.0! This release brings
several exciting new features and improvements to enhance your development
experience.

## ColorPicker (Preview)

The new `ColorPicker` component allows users to select colors in various formats
including HSL, RGB, and HSB. This component is perfect for applications that
need sophisticated color selection capabilities.

<Example name="color-picker-basic" />

## ColorSwatch

Alongside the ColorPicker, we've added the `ColorSwatch` component for
previewing colors. This simple yet powerful component makes it easy to display
color samples in your UI.

<Example name="color-swatch-with-sizes" />

## New Examples

### Input Masking

We showcase how to compose the `use-mask-input` library with the `Input`
component to mask input values. This is particularly useful for formatting phone
numbers and credit card numbers.

<Example name="input-with-mask" />

## CLI Improvements

We've enhanced our CLI with more flexible snippet installation options:

### New --all Flag

By default, the CLI installs a curated set of recommended snippets including
Provider, Avatar, Button, Checkbox, Radio, Input Group, Slider, Popover, Dialog,
Drawer, Tooltip, and Field.

> You can now use the `--all` flag to install all available snippets instead of
> just the recommended ones.

## Bug Fixes

This release also includes important bug fixes:

- Fixed an issue where `mergeConfigs` was mutating the underlying configs
- Resolved a problem where typegen wasn't working when the CLI was installed
  globally or run using `npx`

## Upgrading

To upgrade to the latest version, run:

```bash
npm install @chakra-ui/react@latest
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/blog/03-building-consistent-uis-with-design-tokens.mdx

```text
---
title: Building Consistent UIs with Design Tokens
description: Design tokens allow developers to create maintainable UIs.
type: article
authors: ["esther"]
publishedAt: "2024-11-24"
---

Design tokens allow developers to create maintainable, scalable, and easy to
manage user interfaces.

They create a shared language between designers and developers, allowing them to
reference standardized values instead of hard-coding specific styles. This way,
rather than updating styles individually across components, developers can
adjust token values, which are then applied globally.

In this article, I'll cover how you can leverage Chakra UI's built-in design
tokens to maintain consistency across all your applications.

## What Are Design Tokens?

Design tokens are key-value pairs that represent the visual properties and UI
elements of a design system, such as colors, typography, spacing and more. They
play a key role in allowing you to create scalable, cohesive, and consistent
user interfaces.

They serve as the foundation of a design system, allowing developers and
designers to implement consistent styles across components and projects
efficiently. By abstracting visual properties into tokens, design tokens create
a shared language between design and development teams, ensuring cohesive
designs.

In Chakra UI, these tokens fall into two main categories:

- **Core Tokens**: Fundamental properties such as colors, font sizes, spacing,
  and borders.
- **Semantic Tokens**: Higher-level tokens tied to themes that enable easy style
  updates across dark and light modes or other design contexts.

## Using Core Tokens for Scalability

Chakra UI's [core design tokens](/docs/theming/tokens) include foundational
properties like **colors**, **spacing**, **typography**, **radii**, **borders**
and more, which help you quickly set up your application's look and feel.

Here's how to use some of these tokens to streamline development:

- **Colors**: Chakra UI provides a wide palette of color tokens, ranging from
  `.50` to `.950` such as `gray.50`, `gray.300`, etc.

```jsx
<Box bg="gray.300" p={4}>
  Chakra Box with Core Color Token
</Box>
```

- **Spacing and Sizing**: Tokens for margin, padding and other sizing properties
  allow for consistent layout dimensions.

  By default, Chakra includes a comprehensive numeric spacing scale where the
  values are proportional. So, 1 spacing unit is equal to `0.25rem`, which
  translates to `4px` by default in common browsers.

  Using tokens like 4, 8, 12, etc., ensures a predictable layout behavior.

```jsx
<Box padding={4} margin={2}>
  Consistent Padding and Margin
</Box>
```

> This translates to a padding of `16px` and a margin of `8px`.

- **Typography**: Chakra provides font size and weight tokens, so your app's
  text elements maintain a uniform appearance. By specifying tokens like `lg`,
  `md`, or `xl` for font sizes, you reduce the likelihood of inconsistent text
  styling.

```jsx
<Text fontSize="lg" fontWeight="bold">
  Styled with Font Tokens
</Text>
```

## Customizing Core Tokens

To start [customizing tokens](/docs/theming/customization/colors#tokens), you
need to extend the Chakra theme by using Chakra's `defineConfig` function. This
function allows you to override Chakra's default tokens with your own values.

Let's look at how you can start by creating a custom `theme.ts` file. A design
token in Chakra UI consists of the following properties:

- value: The value of the token. This can be any valid CSS value.
- description: An optional description of what the token can be used for.

```ts
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const customConfig = defineConfig({
  theme: {
    tokens: {
      colors: {
        brand: {
          50: { value: "#e6f2ff" },
          100: { value: "#e6f2ff" },
          200: { value: "#bfdeff" },
          300: { value: "#99caff" },
          // ...
          950: { value: "#001a33" },
        },
      },
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

Now, you can utilize your custom token where needed, such as:

```jsx
<Box bg="brand.100">This Box uses a custom token</Box>
```

## Using semantic tokens for contextual styling

[Semantic tokens](/docs/theming/semantic-tokens) allow for context-based styling
and enables you to change the appearance of your app based on the theme or mode
(e.g., light or dark mode).

```jsx
<Button color="danger">Click me</Button>
```

In most cases, the value of a semantic token references to an existing token.

To reference a value in a semantic token, use the token reference `{}` syntax.

```ts
import { createSystem, defineConfig } from "@chakra-ui/react"

const config = defineConfig({
  theme: {
    tokens: {
      colors: {
        red: { value: "#EE0F0F" },
      },
    },
    semanticTokens: {
      colors: {
        danger: { value: "{colors.red}" },
      },
    },
  },
})

export default createSystem(config)
```

## Customizing the semantic tokens

You can
[create your own semantic tokens](/docs/theming/customization/colors#custom-tokens)
by extending Chakra's theme. This flexibility enables you to customize semantic
tokens based on your brand's unique color scheme.

```jsx
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const customConfig = defineConfig({
  theme: {
    semanticTokens: {
      colors: {
        "checkbox-border": {
          value: { _light: "gray.200", _dark: "gray.800" },
        },
      },
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

## Boost Collaboration with Design Tokens

Design tokens promote a unified approach to styling, which can drastically
improve collaboration between developers and designers. Here's how design tokens
in Chakra UI help:

- **Creating a Shared Visual Language**: Design tokens provide a standard set of
  values for developers and designers, ensuring everyone uses the same color
  palette, spacing, and typography values.

- **Efficiently Managing Global Design Changes**: Because design tokens are
  centrally managed, changing a token value updates all components using that
  token. For example, if you adjust a `primary.500` token to a new shade, every
  component using this token instantly reflects the change.

- **Enhanced Reusability and Maintainability**: With Chakra's tokens, components
  are designed to be reusable, enhancing your code's scalability. When you need
  to adjust a style across multiple components, you simply update the token
  rather than modifying each component individually.

## Conclusion

Whether you're implementing core tokens for standard styles or using semantic
tokens to adapt to theme changes, Chakra UI's design tokens make your interface
clean, efficient, and easier to maintain.

By using a shared set of values, you're not only speeding up your development
workflow but also ensuring your designs stay cohesive. Try it out in your next
project, and experience firsthand how design tokens can simplify your work and
improve collaboration across your team.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/blog/04-chakra-ui-london-meetup-2024.mdx

```text
---
title: Chakra UI London Meetup 2024 Recap
description: Last Thursday's meetup was nothing short of amazing.
type: article
authors: ["esther"]
publishedAt: "2024-12-03"
---

The Chakra UI London Meetup was a special evening where we got to meet
face-to-face with many community members who we had only known through online
interactions in the open source world. Some of these connections spanned months
or even years, making the in-person gathering even more meaningful.

The event held on Thursday, November 28th, 2024 where we talked about Chakra UI
v3, and the future of Chakra UI.

The evening kicked off with light refreshments and chats. We exchanged ideas,
shared our favorite Chakra components, and of course, grabbed some stickers.

<Image
  src="/images/group-photos.png"
  alt="Group photos"
  objectFit="contain"
  fill
  height="500px"
/>

## Talks

We had three amazing talks that evening, each bringing unique insights into
Chakra UI's evolution and new features.

### Building Chakra UI v3

The opening talk was delivered by [Sage](https://x.com/thesegunadebayo), the
creator of Chakra UI. His talk provided a technical deep dive into the process
behind building Chakra UI v3, going from the logic layer to the presentation
layer.

<Image
  src="/images/sage-talk.png"
  alt="Sage Talk"
  objectFit="contain"
  fill
  height="500px"
/>

Key highlights from the talk:

- **Component Design**: Using the slider component, Sage showcased how
  components are now built in Chakra UI. Going from the headless layer to the
  presentation layer, and leveraging state machines to handle interactions.

  - **Logic Layer** powered by [Zag.js](https://zagjs.com/)
  - **Headless UI Layer** handled by [Ark UI](https://ark-ui.com/)
  - **Presentation Layer** handled by Chakra's theming system

- **Component Anatomy**: He broke down the structure of Chakra UI components
  into separate parts so create a shared language for developers and designers.

- **Component API**: Sage emphasized how Chakra UI v3 leverages both open and
  closed component APIs to provide a flexible and consistent developer
  experience.

<ResourceCard
  type="figma"
  title="View the slides"
  url="https://www.figma.com/deck/WbCGjQGQCupXNvCiSqMHsf/Chakra-Meetup-Nov.?node-id=1-25&node-type=slide&viewport=-136%2C-13%2C0.63&t=tAFo6Oa8uI2pxIW5-1&scaling=min-zoom&content-scaling=fixed&page-id=0%3A1"
/>

<br />

### Hidden Gems of Chakra UI v3

Next up, [Ivica Batinić](https://x.com/_isBatak) took the stage to highlight new
features in Chakra v3, showing how the theming system works, and ways to improve
runtime performance of dynamic styles.

<Image
  src="/images/ivica-talk.png"
  alt="Ivica Talk"
  objectFit="contain"
  fill
  height="500px"
/>

Key highlights from the talk:

- **Multipart Component Nuances**: Understanding the finer details of multipart
  components.

- **Performant Dynamic Styles**: Techniques for optimizing styles dynamically.

- **Modernized Polymorphic Patterns**: Leveraging patterns that boost
  flexibility in component usage.

- **Advanced Styling Features**: Unlocking powerful new styling capabilities.

<ResourceCard
  type="site"
  title="View the slides"
  url="https://hidden-gems-of-chakra-ui-v3.vercel.app/1"
/>

<br />

### Exploring Chakra's Color Picker

The final talk of the night was led by
[Esther Adebayo](https://x.com/_estheradebayo), who gave a stunning demo of the
new Color Picker component in Chakra UI v3, showing how to build a page with
real-time color customization.

<Image
  src="/images/esther-talk.png"
  alt="Esther Talk"
  objectFit="contain"
  fill
  height="500px"
/>

Key highlights from the talk:

- **Color Picker Overview**: Why a Color Picker is an essential tool for any
  design system.

- **Design Considerations**: What makes a good color picker component, and how
  to build one.

- **Component Anatomy**: A walkthrough of the Color Picker's anatomy.

- **Live Demo**: Esther demoed the ColorPicker component, showing how it can be
  used to style a login screen in real time.

<ResourceCard
  type="youtube"
  title="Watch the talk"
  url="https://youtu.be/neesBbYuGT0"
/>

<br />

## Conclusion

We wrapped up the evening with a Q&A session where attendees got the chance to
ask their questions, from technical details to what's next for Chakra UI.

A huge thank you to everyone who attended and made this meetup so special. We
couldn't do this without you, and we look forward to the next one.

<Image
  src="/images/stickers.png"
  alt="Chakra Stickers"
  objectFit="contain"
  fill
  height="500px"
/>

## Connect with us

- Join our [Discord community](https://discord.gg/chakra-ui).
- Follow us on [X](https://x.com/chakra_ui).
- Keep an eye out for future events!

<br />

Until next time, keep building with Chakra UI!
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/accordion.mdx

```text
---
title: Accordion
description: Used to show and hide sections of related content on a page
links:
  source: components/accordion
  storybook: components-accordion--basic
  recipe: accordion
  ark: https://ark-ui.com/react/docs/components/accordion
---

<ExampleTabs name="accordion-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`accordion` snippet

```sh
npx @chakra-ui/cli snippet add accordion
```

The snippet includes a closed component composition for the `Accordion`
component.

## Usage

```jsx
import {
  AccordionItem,
  AccordionItemContent,
  AccordionItemTrigger,
  AccordionRoot,
} from "@/components/ui/accordion"
```

```jsx
<AccordionRoot>
  <AccordionItem>
    <AccordionItemTrigger />
    <AccordionItemContent />
  </AccordionItem>
</AccordionRoot>
```

## Examples

### Controlled

Set the accordion that shows up by default.

<ExampleTabs name="accordion-controlled" />

### With Icon

Use the `AccordionItemIcon` to add an icon to each accordion item.

<ExampleTabs name="accordion-with-icon" />

### Expand Multiple Items

Use the `multiple` prop to allow multiple items to be expanded at once.

<ExampleTabs name="accordion-with-multiple" />

### Sizes

Use the `size` prop to change the size of the accordion.

<ExampleTabs name="accordion-sizes" />

### Variants

Use the `variant` prop to change the visual style of the accordion. Values can
be either `outline`, `elevated`, `contained` or `plain`.

<ExampleTabs name="accordion-variants" />

### Disabled Item

Pass the `disabled` prop to disable an accordion item to prevent it from being
expanded or collapsed.

<ExampleTabs name="accordion-with-disabled-item" />

### With Avatar

Here's an example of composing an accordion with an avatar.

<ExampleTabs name="accordion-with-avatar" />

### With Subtext

Here's an example of adding a subtext to an accordion item.

<ExampleTabs name="accordion-with-subtext" />

### With Actions

Here's an example of adding actions to an accordion item trigger.

<ExampleTabs name="accordion-with-actions" />

### Without Snippet

Here's how to setup the accordion without using the snippet.

<ExampleTabs name="accordion-without-snippet" />

## Props

### Root

<PropTable component="Accordion" part="Root" />

### Item

<PropTable component="Accordion" part="Item" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/action-bar.mdx

```text
---
title: Action Bar
description: Used to display a bottom action bar with a set of actions
links:
  source: components/action-bar
  storybook: components-action-bar--basic
  recipe: action-bar
---

<ExampleTabs name="action-bar-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`action-bar` snippet

```sh
npx @chakra-ui/cli snippet add action-bar
```

The snippet includes a closed component composition based on the `Popover`
component.

## Usage

The action bar is designed to be controlled by table or checkbox selections. It
provides a set of actions that can be performed on the selected items.

```jsx
import {
  ActionBarCloseTrigger,
  ActionBarContent,
  ActionBarRoot,
  ActionBarSelectionTrigger,
  ActionBarSeparator,
} from "@/components/ui/action-bar"
```

```jsx
<ActionBarRoot>
  <ActionBarContent>
    <ActionBarCloseTrigger />
    <ActionBarSelectionTrigger />
    <ActionBarSeparator />
    <Button />
  </ActionBarContent>
</ActionBarRoot>
```

## Examples

### Close Trigger

Render the `ActionBarCloseTrigger` to close the action bar, and pass the
`onOpenChange` handler to control the visibility of the action bar.

> The `open` and `onOpenChange` props control the visibility of the action bar.

<ExampleTabs name="action-bar-with-close-trigger" />

### Within Dialog

Here's an example of composing the `ActionBar` and the `Dialog` to perform a
delete action on a set of selected items.

> Press the `Delete projects` button to open the dialog.

<ExampleTabs name="action-bar-with-dialog" />

### Without Snippet

If you don't want to use the snippet, you can use the `ActionBar` component from
the `@chakra-ui/react` package.

<ExampleTabs name="action-bar-without-snippet" />

## Props

### Root

<PropTable component="ActionBar" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/alert.mdx

```text
---
title: Alert
description: Used to communicate a state that affects a system, feature or page.
links:
  source: components/alert
  storybook: components-alert--basic
  recipe: alert
---

<ExampleTabs name="alert-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`alert` snippet

```sh
npx @chakra-ui/cli snippet add alert
```

The snippet includes a closed component composition for the `Alert` component.

## Usage

```jsx
import { Alert } from "@/components/ui/alert"
```

```jsx
<Alert title="Alert Title" icon={<LuTerminal />}>
  This is the alert description
</Alert>
```

## Examples

### Description

Use the `children` prop to provide additional context to the alert. This will be
displayed below the alert message.

<ExampleTabs name="alert-with-description" />

### Status

Change the status of the alerts by passing the `status` prop. This affects the
color scheme and icon used. Alert supports `error`, `success`, `warning`, and
`info` statuses.

<ExampleTabs name="alert-with-status" />

### Variants

Use the `variant` prop to change the visual style of the alert. Values can be
either `subtle`, `solid`, `outline`

<ExampleTabs name="alert-with-variants" />

### Custom Icon

Use the `icon` prop to pass a custom icon to the alert. This will override the
default icon for the alert status.

<ExampleTabs name="alert-with-custom-icon" />

### Without Snippet

Here's an example of how to use the `Alert` without the snippet.

<ExampleTabs name="alert-without-snippet" />

### Color Palette Override

The default colorPalette is inferred from the `status` prop. To override the
color palette, pass the `colorPalette` prop.

<ExampleTabs name="alert-with-color-palette-override" />

## Props

### Root

<PropTable component="Alert" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/aspect-ratio.mdx

```text
---
title: Aspect Ratio
description: Used to embed responsive videos and maps, etc
links:
  source: components/aspect-ratio
  storybook: layout-aspectratio--responsive
---

<ExampleTabs name="aspect-ratio-basic" />

## Usage

```jsx
import { AspectRatio } from "@chakra-ui/react"
```

```jsx
<AspectRatio>
  <iframe
    title="naruto"
    src="https://www.youtube.com/embed/QhBnZ6NPOY0"
    allowFullScreen
  />
</AspectRatio>
```

## Examples

### Image

Here's how to embed an image that has a 4 by 3 aspect ratio.

<ExampleTabs name="aspect-ratio-with-image" />

### Video

To embed a video with a specific aspect ratio, use an iframe with `src` pointing
to the link of the video.

<ExampleTabs name="aspect-ratio-with-video" />

### Google Map

Here's how to embed a responsive Google map using `AspectRatio`.

<ExampleTabs name="aspect-ratio-with-map" />

### Responsive

Here's an example of applying a responsive aspect ratio to a box.

<ExampleTabs name="aspect-ratio-responsive" />

## Props

These props can be passed to the `AspectRatio` component.

<PropTable component="AspectRatio" part="AspectRatio" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/avatar.mdx

```text
---
title: Avatar
description: Used to represent user profile picture or initials
links:
  source: components/avatar
  storybook: components-avatar--basic
  recipe: avatar
  ark: https://ark-ui.com/react/docs/components/avatar
---

<ExampleTabs name="avatar-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`avatar` snippet

```sh
npx @chakra-ui/cli snippet add avatar
```

The snippet includes a closed component composition for the `Avatar` and
`AvatarGroup` components.

## Usage

```jsx
import { Avatar, AvatarGroup } from "@/components/ui/avatar"
```

```jsx
<AvatarGroup>
  <Avatar />
</AvatarGroup>
```

## Examples

### Sizes

Use the `size` prop to change the size of the avatar

<ExampleTabs name="avatar-with-sizes" />

### Variants

Use the `variant` prop to change the variant of the avatar

<ExampleTabs name="avatar-with-variants" />

### Shape

Use the `shape` prop to change the shape of the avatar, from `rounded` to
`square`

<ExampleTabs name="avatar-with-shape" />

### Fallback

The initials of the name can be used as a fallback when the `src` prop is not
provided or when the image fails to load.

<ExampleTabs name="avatar-with-fallback" />

### Random Color

Combine the `colorPalette` prop with some custom logic to dynamically change the
color of the avatar

<ExampleTabs name="avatar-with-random-color" />

### Ring

Use the `outline*` props to add a ring around the avatar

<ExampleTabs name="avatar-with-ring" />

### Group

Use the `Group` component to group multiple avatars together

<ExampleTabs name="avatar-with-group" />

### Stacking

When using the `AvatarGroup` component, you can use the `stacking` prop to
change the stacking order of the avatars

<ExampleTabs name="avatar-group-with-stacking" />

### Persona

Here's an example of how to use the `Avatar` component to display a user
persona.

<ExampleTabs name="avatar-persona" />

### Badge

Show a badge on the right corner of the avatar by composing the `Float` and
`Circle` components

<ExampleTabs name="avatar-with-badge" />

### Overflow

Here's an example of how to handle an overflow of avatars by composing the
`Menu` and `Avatar` components.

<ExampleTabs name="avatar-with-overflow" />

### Without Snippet

Here's an example of how to use the `Avatar` without the snippet.

<ExampleTabs name="avatar-without-snippet" />

## Props

### Root

<PropTable component="Avatar" part="Root" />

### Fallback

<PropTable component="Avatar" part="Fallback" />

### Image

<PropTable component="Avatar" part="Image" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/badge.mdx

```text
---
title: Badge
description: Used to highlight an item's status for quick recognition.
links:
  source: components/badge
  storybook: components-badge--basic
  recipe: badge
---

<ExampleTabs name="badge-basic" />

## Usage

```jsx
import { Badge } from "@chakra-ui/react"
```

```jsx
<Badge>Badge</Badge>
```

## Examples

### Icon

Render an icon within the badge directly

<ExampleTabs name="badge-with-icon" />

### Variants

Badges come in different variants

<ExampleTabs name="badge-with-variants" />

### Sizes

Badges come in different sizes

<ExampleTabs name="badge-with-sizes" />

## Props

<PropTable component="Badge" part="Badge" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/bleed.mdx

```text
---
title: Bleed
description: Used to break an element from the boundaries of its container
links:
  source: components/bleed
  storybook: layout-bleed--basic
---

<ExampleTabs name="bleed-basic" />

## Usage

```jsx
import { Bleed } from "@chakra-ui/react"
```

```jsx
<Bleed>
  <div />
</Bleed>
```

## Examples

### Vertical

Use the `block` prop to make the element bleed vertically.

<ExampleTabs name="bleed-vertical" />

### Specific Direction

Use the `inlineStart`, `inlineEnd`, `blockStart`, and `blockEnd` props to make
the element bleed in a specific direction.

<ExampleTabs name="bleed-with-direction" />

## Props

<PropTable component="Bleed" part="Bleed" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/blockquote.mdx

```text
---
title: Blockquote
description: Used to quote text content from an external source
links:
  source: components/blockquote
  storybook: components-blockquote--basic
  recipe: blockquote
---

<ExampleTabs name="blockquote-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`blockquote` snippet

```sh
npx @chakra-ui/cli snippet add blockquote
```

The snippet includes a closed component composition for the `Blockquote`
component.

```jsx
import { Blockquote } from "@/components/ui/blockquote"
```

```jsx
<Blockquote dash cite="Uzumaki Naruto" citeUrl="#" />
```

## Examples

### With Cite

Use the `cite` prop to provide the source of the blockquote. This will be
displayed below the blockquote.

<ExampleTabs name="blockquote-with-cite" />

### Colors

Use the `colorPalette` prop to change the color of the blockquote.

<ExampleTabs name="blockquote-with-colors" />

### Variants

Use the `variant` prop to change the visual style of the blockquote. Values can
be either `subtle`, `solid`, `plain`.

<ExampleTabs name="blockquote-with-variants" />

### Icon

Use the `showIcon` prop to show an icon on the blockquote. The default icon is a
double quote.

<ExampleTabs name="blockquote-with-icon" />

### Justify

Use the `justify` prop to change the alignment of the blockquote. Values can be
either `start`, `center`, `end`.

<ExampleTabs name="blockquote-with-justify" />

### Custom Icon

Use the `icon` prop to change the icon of the blockquote.

<ExampleTabs name="blockquote-with-custom-icon" />

### With Avatar

Here's an example of how to use the `Blockquote` with an avatar and float
components to create a stunning testimonial component.

<ExampleTabs name="blockquote-with-avatar" />

### Without Snippet

If you don't want to use the snippet, you can use the `Blockquote` component
from the `@chakra-ui/react` package.

<ExampleTabs name="blockquote-without-snippet" />

## Props

### Root

<PropTable component="Blockquote" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/box.mdx

```text
---
title: Box
description:
  The most abstract styling component in Chakra UI on top of which all other
  Chakra UI components are built.
---

<ExampleTabs name="box-basic" />

## Usage

The `Box` component provides an easy way to write styles with ease. It provides
access to design tokens and an unmatched DX when writing responsive styles.

```jsx
import { Box } from "@chakra-ui/react"
```

```jsx
<Box />
```

## Examples

### Shorthand

Use shorthand like `bg` instead of `backgroundColor`, `m` instead of `margin`,
etc.

<ExampleTabs name="box-with-shorthand" />

### Pseudo Props

Use pseudo props like `_hover` to apply styles on hover, `_focus` to apply
styles on focus, etc.

<ExampleTabs name="box-with-pseudo-props" />

### Border

Use the `borderWidth` and `borderColor` prop to apply border styles.

> **Good to know:** Chakra applies `borderStyle: solid` globally so you don't
> have to.

<ExampleTabs name="box-with-border" />

### As Prop

Use the `as` prop to render a different component.

> Inspect the DOM to see the rendered component.

<ExampleTabs name="box-with-as-prop" />

### Shadow

Use the `boxShadow` or `shadow` prop to apply shadow styles.

<ExampleTabs name="box-with-shadow" />

### Composition

Here's an example of a property card built with layout primitives in Chakra.

<ExampleTabs name="box-property-card" />

## Props

The `Box` component supports all CSS properties as props, making it easy to
style elements.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/breadcrumb.mdx

```text
---
title: Breadcrumb
description:
  Used to display a page's location within a site's hierarchical structure
links:
  source: components/breadcrumb
  storybook: components-breadcrumb--basic
  recipe: breadcrumb
---

<ExampleTabs name="breadcrumb-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`breadcrumb` snippet

```sh
npx @chakra-ui/cli snippet add breadcrumb
```

The snippet includes a closed component composition for the `Breadcrumb`
component.

## Usage

```jsx
import {
  BreadcrumbCurrentLink,
  BreadcrumbLink,
  BreadcrumbRoot,
} from "@/components/ui/breadcrumb"
```

```jsx
<BreadcrumbRoot>
  <BreadcrumbLink href="#">Docs</BreadcrumbLink>
  <BreadcrumbLink href="#">Components</BreadcrumbLink>
  <BreadcrumbCurrentLink>Props</BreadcrumbCurrentLink>
</BreadcrumbRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the breadcrumb component

<ExampleTabs name="breadcrumb-with-sizes" />

### Variants

Use the `variant` prop to change the appearance of the breadcrumb component

<ExampleTabs name="breadcrumb-with-variants" />

### With Separator

Use the `separator` prop to add the separator

<ExampleTabs name="breadcrumb-with-separator" />

### Icon

Here's how you can add an icon to the breadcrumb

<ExampleTabs name="breadcrumb-with-icon" />

### Menu

Compose the breadcrumb with menu component

<ExampleTabs name="breadcrumb-with-menu" />

### Ellipsis

Render the `BreadcrumbEllipsis` component to show an ellipsis

<ExampleTabs name="breadcrumb-with-ellipsis" />

### Routing Library

Use the `asChild` prop to change the underlying breadcrumb link

```jsx
import { Link } from "next/link"

export const Example = () => {
  return (
    <BreadcrumbRoot>
      <BreadcrumbLink asChild>
        <Link href="/docs">Docs</Link>
      </BreadcrumbLink>
    </BreadcrumbRoot>
  )
}
```

### Without Snippet

If you don't want to use the snippet, you can use the `Breadcrumb` component
from the `@chakra-ui/react` package.

<ExampleTabs name="breadcrumb-without-snippet" />

## Props

### Root

<PropTable component="Breadcrumb" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/button.mdx

```text
---
title: Button
description: Used to trigger an action or event
links:
  source: components/button
  storybook: components-button--basic
  recipe: button
---

<ExampleTabs name="button-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`button` snippet

```sh
npx @chakra-ui/cli snippet add button
```

The snippet includes enhances the Button with `loading` and `loadingText` props.

## Usage

```jsx
import { Button } from "@/components/ui/button"
```

```jsx
<Button>Click me</Button>
```

## Examples

### Sizes

Use the `size` prop to change the size of the button.

<ExampleTabs name="button-with-sizes" />

### Variants

Use the `variant` prop to change the visual style of the Button.

<ExampleTabs name="button-with-variants" />

### Icon

Use icons within a button

<ExampleTabs name="button-with-icons" />

### Color

Use the `colorPalette` prop to change the color of the button

<ExampleTabs name="button-with-colors" />

### Loading

Use the `loading` and `loadingText` prop to show a loading spinner

<ExampleTabs name="button-with-loading" />

### Group

Use the `Group` component to group buttons together

<ExampleTabs name="button-with-group" />

## Props

<PropTable component="Button" part="Button" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/card.mdx

```text
---
title: Card
description: Used to display content related to a single subject.
links:
  source: components/card
  storybook: components-card--basic
  recipe: card
---

<ExampleTabs name="card-basic" />

## Usage

```jsx
import { Card } from "@chakra-ui/react"
```

```jsx
<Card.Root>
  <Card.Header />
  <Card.Body />
  <Card.Footer />
</Card.Root>
```

## Examples

### Variants

Use the `variant` prop to change the visual style of the Card.

<ExampleTabs name="card-with-variants" />

### Within Form

Use the Card component within a form to group related fields together.

<ExampleTabs name="card-with-form" />

### Sizes

Use the `size` prop to change the size of the Card.

<ExampleTabs name="card-with-sizes" />

### With Image

Use the Card component to display an image.

<ExampleTabs name="card-with-image" />

### Horizontal

Use the Card component to display content horizontally.

<ExampleTabs name="card-horizontal" />

### With Avatar

Use the Card component to display an avatar.

<ExampleTabs name="card-with-avatar" />

## Props

### Root

<PropTable component="Card" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/center.mdx

```text
---
title: Center
description: Used to center its child within itself.
links:
  source: components/center
  storybook: layout-center--basic
---

<ExampleTabs name="center-basic" />

## Usage

```jsx
import { AbsoluteCenter, Center, Circle, Square } from "@chakra-ui/react"
```

```jsx
<Center bg="tomato" h="100px" color="white">
  This is the Center
</Center>
```

## Examples

### Icon

Center can be used to create frames around icons or numbers.

<ExampleTabs name="center-with-icons" />

### Center with Inline

Use the `inline` to change the display to `inline-flex`.

<ExampleTabs name="center-with-inline" />

### Square

`Square` centers its child given the `size` (width and height).

<ExampleTabs name="center-with-square" />

### Circle

`Circle` centers its child given the `size` and creates a circle around it.

<ExampleTabs name="center-with-circle" />

### AbsoluteCenter

`AbsoluteCenter` centers relative to its parent using the `position: absolute`
strategy. Pass the `axis` prop to change the axis of alignment.

<ExampleTabs name="center-with-absolute" />

## Props

### AbsoluteCenter

<PropTable component="Center" part="AbsoluteCenter" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/checkbox-card.mdx

```text
---
title: Checkbox Card
description: Used to select or deselect options displayed within cards.
links:
  source: components/checkbox-card
  storybook: components-checkbox-card--basic
  recipe: checkbox-card
  ark: https://ark-ui.com/react/docs/components/checkbox
---

<ExampleTabs name="checkbox-card-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`checkbox-card` snippet

```sh
npx @chakra-ui/cli snippet add checkbox-card
```

The snippet includes a closed component composition for the `CheckboxCard`
component.

## Usage

```jsx
import { CheckboxCard } from "@/components/ui/checkbox-card"
```

```jsx
<CheckboxCard label="Checkbox Card" />
```

## Examples

### Group

Use the `CheckboxCardGroup` component to group multiple checkbox cards.

<ExampleTabs name="checkbox-card-with-group" />

### Sizes

Use the `size` prop to change the size of the checkbox card.

<ExampleTabs name="checkbox-card-with-sizes" />

### Variants

Use the `variant` prop to change the variant of the checkbox card.

<ExampleTabs name="checkbox-card-with-variants" />

### Disabled

Use the `disabled` prop to make the checkbox card disabled.

<ExampleTabs name="checkbox-card-disabled" />

### Addon

Use the `CheckboxCardAddon` to add some more context to the checkbox card.

<ExampleTabs name="checkbox-card-with-addon" />

### Icon

Render custom icons within the checkbox card.

<ExampleTabs name="checkbox-card-with-icon" />

### Without Snippet

If you don't want to use the snippet, you can use the `CheckboxCard` component
from the `@chakra-ui/react` package.

<ExampleTabs name="checkbox-card-without-snippet" />

## Props

### Root

<PropTable component="CheckboxCard" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/checkbox.mdx

```text
---
title: Checkbox
description:
  Used in forms when a user needs to select multiple values from several options
links:
  source: components/checkbox
  storybook: components-checkbox--basic
  recipe: checkbox
  ark: https://ark-ui.com/react/docs/components/checkbox
---

<ExampleTabs name="checkbox-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`checkbox` snippet

```sh
npx @chakra-ui/cli snippet add checkbox
```

The snippet includes a closed component composition for the `Checkbox`

## Usage

```jsx
import { Checkbox } from "@/components/ui/checkbox"
```

```jsx
<Checkbox>Click me</Checkbox>
```

## Examples

### Variants

Use the `variant` prop to change the visual style of the checkbox.

<ExampleTabs name="checkbox-with-variants" />

### Controlled

Use the `checked` and `onCheckedChange` props to control the state of the
checkbox.

<ExampleTabs name="checkbox-controlled" />

### Colors

Use the `colorPalette` prop to change the color of the checkbox.

<ExampleTabs name="checkbox-with-colors" />

### Sizes

Use the `size` prop to change the size of the checkbox.

<ExampleTabs name="checkbox-with-sizes" />

### States

Use the `disabled` or `invalid` prop to change the visual state of the checkbox.

<ExampleTabs name="checkbox-with-states" />

### Hook Form

Here's an example of how to use the `Checkbox` component with the
`react-hook-form` library.

<ExampleTabs name="checkbox-with-hook-form" />

### Group

Use the `CheckboxGroup` component to group multiple checkboxes together.

<ExampleTabs name="checkbox-with-group" />

### Group Hook Form

Here's an example of how to use the `CheckboxGroup` component with the
`react-hook-form` library.

<ExampleTabs name="checkbox-with-group-hook-form" />

### Custom Icon

Use the `icon` prop to change the icon of the checkbox.

<ExampleTabs name="checkbox-with-custom-icon" />

### Indeterminate

Set the `checked` prop to `indeterminate` to show the checkbox in an
indeterminate state.

<ExampleTabs name="checkbox-indeterminate" />

### Description

Add content to the children of the `Checkbox` component to add a description.

<ExampleTabs name="checkbox-with-description" />

### Link

Render an anchor tag as the checkbox label.

<ExampleTabs name="checkbox-with-link" />

### Without Snippet

If you don't want to use the snippet, you can use the `Checkbox` component from
the `@chakra-ui/react` package.

<ExampleTabs name="checkbox-without-snippet" />

## Props

### Root

<PropTable component="Checkbox" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/client-only.mdx

```text
---
title: Client Only
description: Used to render content only on the client side.
links:
  source: components/client-only
---

## Usage

```jsx
import { ClientOnly, Skeleton } from "@chakra-ui/react"
```

```jsx
<ClientOnly fallback={<Skeleton />}>
  <ColorModeButton />
</ClientOnly>
```

## Props

These props can be passed to the `ClientOnly` component.

<PropTable component="ClientOnly" part="ClientOnly" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/clipboard.mdx

```text
---
title: Clipboard
description: Used to copy text to the clipboard
links:
  source: components/clipboard
  storybook: components-clipboard--basic
  recipe: clipboard
  ark: https://ark-ui.com/react/docs/components/clipboard
---

<ExampleTabs name="clipboard-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`clipboard` snippet

```sh
npx @chakra-ui/cli snippet add clipboard
```

The snippet includes a closed component composition for the `Clipboard`
component.

## Usage

```jsx
import {
  ClipboardButton,
  ClipboardIconButton,
  ClipboardInput,
} from "@/components/ui/clipboard"
```

```jsx
<ClipboardRoot value="...">
  <ClipboardButton />
  <ClipboardIconButton />
  <ClipboardInput />
</ClipboardRoot>
```

## Examples

### Copy Button

Use the `Clipboard.Context` and `Clipboard.Trigger` components to create a copy
button.

<ExampleTabs name="clipboard-with-button" />

### Input

Use the `Clipboard.Input` component to create a copy input.

<ExampleTabs name="clipboard-with-input" />

### Timeout

Use the `timeout` prop to change the duration of the copy message.

<ExampleTabs name="clipboard-with-timeout" />

### Link Appearance

Use the `ClipboardText` component to create a link appearance.

<ExampleTabs name="clipboard-with-link" />

### Without Snippet

If you don't want to use the snippet, you can use the `Clipboard` component from
the `@chakra-ui/react` package.

<ExampleTabs name="clipboard-without-snippet" />

## Props

### Root

<PropTable component="Clipboard" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/close-button.mdx

```text
---
title: Close Button
description: Used to trigger close functionality
links:
  storybook: components-close-button--basic
  recipe: close-button
---

<ExampleTabs name="close-button-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`close-button` snippet

```sh
npx @chakra-ui/cli snippet add close-button
```

The snippet includes a `CloseButton` component composition based on the
`IconButton` component.

## Usage

```jsx
import { CloseButton } from "@/components/ui/close-button"
```

```jsx
<CloseButton />
```

## Examples

### Sizes

Use the `size` prop to change the size of the close button.

<ExampleTabs name="close-button-with-sizes" />

### Variants

Use the `variant` prop to change the appearance of the close button.

<ExampleTabs name="close-button-with-variants" />

### Without Snippet

If you don't want to use the snippet, you can use the `IconButton` component
from the `@chakra-ui/react` package.

<ExampleTabs name="close-button-without-snippet" />

## Props

<PropTable component="Button" part="Button" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/code.mdx

```text
---
title: Code
description: Used to display inline code
links:
  source: components/code
  storybook: typography-code--basic
  recipe: code
---

<ExampleTabs name="code-basic" />

## Usage

```js
import { Code } from "@chakra-ui/react"
```

```jsx
<Code>Hello world</Code>
```

## Examples

### Sizes

Use the `size` prop to change the size of the code component.

<ExampleTabs name="code-with-sizes" />

### Variants

Use the `variant` prop to change the appearance of the code component.

<ExampleTabs name="code-with-variants" />

### Colors

Use the `colorPalette` prop to change the color scheme of the component.

<ExampleTabs name="code-with-colors" />

## Props

<PropTable component="Code" part="Code" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/collapsible.mdx

```text
---
title: Collapsible
description: Used to expand and collapse additional content.
links:
  source: components/collapsible
  storybook: components-collapsible--basic
  recipe: collapsible
  ark: https://ark-ui.com/react/docs/components/collapsible
---

<ExampleTabs name="collapsible-basic" />

## Usage

```jsx
import { Collapsible } from "@chakra-ui/react"
```

```jsx
<Collapsible.Root>
  <Collapsible.Trigger />
  <Collapsible.Content />
</Collapsible.Root>
```

## Examples

### Lazy Mounted

Use the `unmountOnExit` prop to make the content unmount when collapsed.

<ExampleTabs name="collapsible-lazy-mounted" />

## Props

### Root

<PropTable component="Collapsible" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/color-picker.mdx

```text
---
title: Color Picker
description:
  Used to select colors from a color area or a set of defined swatches
links:
  source: components/color-picker
  storybook: components-color-picker--basic
  recipe: color-picker
  ark: https://ark-ui.com/react/docs/components/color-picker
---

<ExampleTabs name="color-picker-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`color-picker` snippet

```sh
npx @chakra-ui/cli snippet add color-picker
```

The snippet includes a closed component composition for the `ColorPicker`
component.

## Usage

```jsx
import {
  ColorPickerArea,
  ColorPickerContent,
  ColorPickerControl,
  ColorPickerEyeDropper,
  ColorPickerInput,
  ColorPickerLabel,
  ColorPickerRoot,
  ColorPickerSliders,
  ColorPickerTrigger,
} from "@/components/ui/color-picker"
```

```jsx
<ColorPickerRoot>
  <ColorPickerLabel />
  <ColorPickerControl>
    <ColorPickerInput />
    <ColorPickerTrigger />
  </ColorPickerControl>
  <ColorPickerContent>
    <ColorPickerArea />
    <ColorPickerEyeDropper />
    <ColorPickerSliders />
  </ColorPickerContent>
</ColorPickerRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the color picker.

<ExampleTabs name="color-picker-with-sizes" />

### Variants

Use the `variant` prop to change the visual style of the color picker. Values
can be either `outline` or `subtle`.

<ExampleTabs name="color-picker-with-variants" />

### Input Only

Combine the `ColorPickerValueSwatch` and the `ColorPickerEyeDropper` on the
`InputGroup` to render a color picker that contains only an input.

<ExampleTabs name="color-picker-input-only" />

### Swatch Only

Use the `ColorPickerSwatchGroup` and `ColorPickerSwatchTrigger` to render only
the color swatches.

<ExampleTabs name="color-picker-swatch-only" />

### Trigger Only

Compose the color picker to initially show only a trigger using the
`ColorPickerValueSwatch` and `ColorPickerValueText`.

<ExampleTabs name="color-picker-trigger-only" />

### Trigger Inside Input

Compose the color picker to trigger in input using the `InputGroup` and
`ColorPickerInput`.

<ExampleTabs name="color-picker-trigger-only" />

### Controlled

Use the `value` and `onValueChange` props to control the state of the color
picker.

<ExampleTabs name="color-picker-controlled" />

### Change End

Use the `onValueChangeEnd` to listen to when the user finishes selecting a
color, rather than while they are scrubbing or dragging through the color area.

<ExampleTabs name="color-picker-change-end" />

### Channel Slider

Combine the `ColorPickerChannelSliders` and the `format` prop to add the
different color channels to the color picker.

<ExampleTabs name="color-picker-channel-slider-only" />

### Hook Form

Here's an example of how to integrate the color picker with `react-hook-form`.

<ExampleTabs name="color-picker-with-hook-form" />

### Inline

Use the `ColorPickerInlineContent` and pass `open` to the `ColorPickerRoot` to
display an inline version of the color picker.

<ExampleTabs name="color-picker-inline" />

### Disabled

Pass the `disabled` prop to disable the color picker.

<ExampleTabs name="color-picker-with-disabled" />

### Channel Input

Use the `ChannelFormatSelect` and `ColorPickerChannelInputs` to create a color
picker that allows users to select their preferred channel.

<ExampleTabs name="color-picker-with-channel-input" />

### Fit Content

Here's an example of how to create a rounded trigger that fits the content.

<ExampleTabs name="color-picker-with-fit-content" />

### ReadOnly

Use the `readOnly` prop to make the color picker component read-only.

### Save Swatch

Here's an example of how to save a selected color as a swatch.

<ExampleTabs name="color-picker-with-save-swatch" />

### Swatches

Here's an example of how to combine the color picker with pre-defined swatches.

<ExampleTabs name="color-picker-with-swatches" />

### Swatch and Input

Here's how to compose a swatch with an input.

<ExampleTabs name="color-picker-with-swatch-and-input" />

### Swatch and Trigger

Here's how to compose a swatch with a trigger.

<ExampleTabs name="color-picker-with-swatch-and-input" />

## Props

### Root

<PropTable component="ColorPicker" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/color-swatch.mdx

```text
---
title: Color Swatch
description: Used to preview a color
links:
  source: components/color-swatch
  storybook: components-color-swatch--basic
  recipe: color-swatch
---

<ExampleTabs name="color-swatch-basic" />

## Usage

```jsx
import { ColorSwatch } from "@/components/ui/color-swatch"
```

```jsx
<ColorSwatch />
```

## Examples

### Sizes

Use the `size` prop to change the size of the color swatch.

<ExampleTabs name="color-swatch-with-sizes" />

### Alpha

Here's an example of how to create a color swatch with an alpha channel.

<ExampleTabs name="color-swatch-with-alpha" />

### With Badge

Here's an example of how to compose the `ColorSwatch` with a `Badge`.

<ExampleTabs name="color-swatch-with-badge" />

### Mixed Colors

Use the `ColorSwatchMix` to create a color swatch that contains multiple colors,
but retains the size of a single color swatch.

<ExampleTabs name="color-swatch-mixed" />

### Palette

Here's an example of composing multiple swatches to create a palette.

<ExampleTabs name="color-swatch-palette" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/concepts/animation.mdx

```text
---
title: Animation
description: Using CSS animations to animate Chakra UI components
---

We recommend using CSS animations to animate your Chakra UI components. This
approach is performant, straightforward and provides a lot of flexibility.

You can animate both the mounting and unmounting phases of your components with
better control.

## Enter animation

When a disclosure component (popover, dialog) is open, the `data-state`
attribute is set to `open`. This maps to `data-state=open` and can be styled
with `_open` pseudo prop.

```tsx
<Box
  data-state="open"
  _open={{
    animation: "fade-in 300ms ease-out",
  }}
>
  This is open
</Box>
```

Here's an example that uses keyframes to create a fade-in animation:

```css
@keyframes fade-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
```

## Exit animation

When a disclosure component (popover, dialog) is closed, the `data-state`
attribute is set to `closed`. This maps to `data-state=closed` and can be styled
with `_closed` pseudo prop.

```tsx
<Box
  data-state="closed"
  _closed={{
    animation: "fadeOut 300ms ease-in",
  }}
>
  This is closed
</Box>
```

Here's an example that uses keyframes to create a fade-out animation:

```css
@keyframes fadeOut {
  from {
    opacity: 1;
  }
  to {
    opacity: 0;
  }
}
```

## Composing animations

Use the `animationName` prop to compose multiple animations together. This makes
it easy to create complex animations with multiple keyframes.

```tsx
<Box
  data-state="open"
  _open={{
    animationName: "fade-in, scale-in",
    animationDuration: "300ms",
  }}
  _closed={{
    animationName: "fade-out, scale-out",
    animationDuration: "120ms",
  }}
>
  This is a composed animation
</Box>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/concepts/composition.mdx

```text
---
title: Composition
description: Learn how to compose components in Chakra UI.
---

## The `as` Prop

Used to change the underlying HTML element that a React component renders. It
provides a straightforward way to change the underlying element while retaining
the component's functionality.

```jsx
<Heading as="h3">Hello, world!</Heading>
```

:::warning

**TypeScript:** The caveat with the `as` prop is that the types of the component
passed to the `as` prop must be compatible with the component's props. We do not
infer the underlying component's props from the `as` prop.

:::

## The `asChild` Prop

Used to compose a component's functionality onto its child element. This
approach, inspired by
[Radix UI](https://www.radix-ui.com/primitives/docs/utilities/slot), offers
maximum flexibility.

```jsx
<Popover.Root>
  <Popover.Trigger asChild>
    <Button>Open</Button>
  </Popover.Trigger>
</Popover.Root>
```

In this example, the `asChild` prop allows the `Button` to be used as the
trigger for the popover.

## Best Practices

To avoid common pitfalls when using the `as` and `asChild` props, there are a
few best practices to consider:

- **Forward Refs:** Ensure that the underlying component forwards the ref passed
  to it properly.
- **Spread Props:** Ensure that the underlying component spreads the props
  passed to it.

```jsx
const MyComponent = React.forwardRef((props, ref) => {
  return <Box ref={ref} {...props} />
})

// with `as` prop
<MyComponent as="button" />

// with `asChild` prop
<Button asChild>
  <MyComponent> Click me </MyComponent>
</Button>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/concepts/overview.mdx

```text
---
title: Components
description: Accessible, modern and easy to style UI components.
hideToc: true
---

Here's a list of all the components available in the library.

<ComponentGrid />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/concepts/server-components.mdx

```text
---
title: Server Components
description: Learn how to use Chakra UI with React Server Components.
---

React Server Components is a new feature in React that allows you to build
components that render on the server and return UI to the client without hydration.

Client components are still server-rendered but hydrated on the client. Learn
more about
[Server component patterns](https://nextjs.org/docs/app/building-your-application/rendering/composition-patterns)

Chakra UI components are client components because they rely on `useState`,
`useRef` and `useState` which are not available in server components.

:::info

**TLDR:** By default, Chakra UI components can be used with React Server
Components without adding the 'use client' directive.

:::

## Usage

Here's an example of how to use Chakra UI components with React Server
Components in Next.js

```jsx [app/page.tsx]
import { Heading } from "@chakra-ui/react"
import fs from "node:fs"

export default async function Page() {
  const content = fs.readFileSync("path/to/file.md", "utf-8")
  return <Heading as="h1">{content}</Heading>
}
```

## Chakra Factory

When using the `chakra()` factory function, use the `use client` directive and
move the component to a dedicated file.

```jsx [blog-post.tsx]
"use client"

import { chakra } from "@chakra-ui/react"

export const BlogPost = chakra("div", {
  base: {
    color: "red",
  },
  variants: {
    primary: {
      true: { color: "blue" },
      false: { color: "green" },
    },
  },
})
```

Then import the component in your page server component

```jsx [blogs/page.tsx]
import { BlogPost } from "./blog-post"

export default async function Page() {
  const content = fs.readFileSync("path/to/file.md", "utf-8")
  return <BlogPost>{content}</BlogPost>
}
```

## Hooks

When importing hooks from Chakra UI, use the `use client` directive

```jsx
"use client"

import { useBreakpointValue } from "@chakra-ui/react"

export function MyComponent() {
  const value = useBreakpointValue({ base: "mobile", md: "desktop" })
  return <div>{value}</div>
}
```

## Render Props

When using render props, use the `use client` directive

```jsx
"use client"

import { ProgressContext } from "@chakra-ui/react"

export function MyComponent() {
  return <ProgressContext>{({ value }) => <div>{value}</div>}</ProgressContext>
}
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/container.mdx

```text
---
title: Container
description:
  Used to constrain a content's width to the current breakpoint, while keeping
  it fluid.
links:
  source: components/container
  storybook: layout-container--basic
  recipe: container
---

<ExampleTabs name="container-basic" />

## Usage

The default `maxWidth` is `8xl` which maps to `90rem (1440px)`.

```jsx
import { Container } from "@chakra-ui/react"
```

```jsx
<Container>
  <div />
</Container>
```

## Examples

### Sizes

Use the `maxWidth` prop to change the size of the container.

<ExampleTabs name="container-with-sizes" />

### Fluid

Use the `fluid` prop to make the container stretch to fill the width of its
parent.

<ExampleTabs name="container-with-fluid" />

## Props

<PropTable component="Container" part="Container" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/data-list.mdx

```text
---
title: DataList
description: Used to display a list of similar data items.
links:
  source: components/data-list
  storybook: components-data-list--basic
  recipe: data-list
---

<ExampleTabs name="data-list-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`data-list` snippet

```sh
npx @chakra-ui/cli snippet add data-list
```

The snippet includes a closed component composition for the `DataList`
component.

## Usage

```jsx
import { DataListItem, DataListRoot } from "@/components/ui/data-list"
```

```jsx
<DataListRoot>
  {data.map((item) => (
    <DataListItem key={item.label} label={item.label} value={item.value} />
  ))}
</DataListRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the datalist component.

<ExampleTabs name="data-list-with-sizes" />

### Variants

Use the `variant` prop to change the variant of the datalist component.

> Added in `v3.1.x`

<ExampleTabs name="data-list-with-variants" />

### Orientation

Use the `orientation` prop to change the orientation of the datalist component.

<ExampleTabs name="data-list-vertical" />

### Info Tip

Use the `info` prop on the `DataListItem` to provide additional context to the
datalist.

<ExampleTabs name="data-list-with-info" />

### Separator

Use the `divideY` prop on the `DataListRoot` to add a separator between items.

<ExampleTabs name="data-list-with-separator" />

## Props

### Root

<PropTable component="DataList" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/dialog.mdx

```text
---
title: Dialog
description: Used to display a dialog prompt
links:
  source: components/dialog
  storybook: components-dialog--basic
  recipe: dialog
  ark: https://ark-ui.com/react/docs/components/dialog
---

<ExampleTabs name="dialog-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`dialog` snippet

```sh
npx @chakra-ui/cli snippet add dialog
```

The snippet includes a closed component composition for the `Dialog` component.

## Usage

```jsx
import {
  DialogBody,
  DialogCloseTrigger,
  DialogContent,
  DialogFooter,
  DialogHeader,
  DialogRoot,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog"
```

```jsx
<DialogRoot>
  <DialogBackdrop />
  <DialogTrigger />
  <DialogContent>
    <DialogCloseTrigger />
    <DialogHeader>
      <DialogTitle />
    </DialogHeader>
    <DialogBody />
    <DialogFooter />
  </DialogContent>
</DialogRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the dialog component.

<ExampleTabs name="dialog-with-sizes" />

### Cover

Use the `size="cover"` prop to make the dialog component cover the entire screen
while revealing a small portion of the page behind.

<ExampleTabs name="dialog-with-cover" />

### Fullscreen

Use the `size="full"` prop to make the dialog component take up the entire
screen.

<ExampleTabs name="dialog-with-fullscreen" />

### Placement

Use the `placement` prop to change the placement of the dialog component.

<ExampleTabs name="dialog-with-placement" />

### Controlled

Use the `open` and `onOpenChange` prop to control the visibility of the dialog
component.

<ExampleTabs name="dialog-controlled" />

### Initial Focus

Use the `initialFocusEl` prop to set the initial focus of the dialog component.

<ExampleTabs name="dialog-with-initial-focus" />

### Inside Scroll

Use the `scrollBehavior=inside` prop to change the scroll behavior of the dialog
when its content overflows.

<ExampleTabs name="dialog-with-inside-scroll" />

### Outside Scroll

Use the `scrollBehavior=outside` prop to change the scroll behavior of the
dialog when its content overflows.

<ExampleTabs name="dialog-with-outside-scroll" />

### Motion Preset

Use the `motionPreset` prop to change the animation of the dialog component.

<ExampleTabs name="dialog-with-motion-preset" />

### Alert Dialog

Set the `role: "alertdialog"` prop to change the dialog component to an alert
dialog.

<ExampleTabs name="dialog-with-role" />

### Close Button Outside

Here's an example of how to customize the `DialogCloseTrigger` component to
position the close button outside the dialog component.

<ExampleTabs name="dialog-with-close-outside" />

### With DataList

Here's an example of how to compose the dialog component with the `DataList`
component.

<ExampleTabs name="dialog-with-datalist" />

## Props

### Root

<PropTable component="Dialog" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/drawer.mdx

```text
---
title: Drawer
description:
  Used to render a content that slides in from the side of the screen.
links:
  source: components/drawer
  storybook: components-drawer--basic
  recipe: drawer
  ark: https://ark-ui.com/react/docs/components/dialog
---

<ExampleTabs name="drawer-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`drawer` snippet

```sh
npx @chakra-ui/cli snippet add drawer
```

The snippet includes a closed component composition for the `Drawer` component.

## Usage

```jsx
import {
  DrawerBackdrop,
  DrawerBody,
  DrawerCloseTrigger,
  DrawerContent,
  DrawerFooter,
  DrawerHeader,
  DrawerRoot,
  DrawerTitle,
  DrawerTrigger,
} from "@/components/ui/drawer"
```

```jsx
<DrawerRoot>
  <DrawerBackdrop />
  <DrawerTrigger />
  <DrawerContent>
    <DrawerCloseTrigger />
    <DrawerHeader>
      <DrawerTitle />
    </DrawerHeader>
    <DrawerBody />
    <DrawerFooter />
  </DrawerContent>
</DrawerRoot>
```

## Examples

### Controlled

Use the `open` and `onOpenChange` props to control the drawer component.

<ExampleTabs name="drawer-controlled" />

### Sizes

Use the `size` prop to change the size of the drawer component.

<ExampleTabs name="drawer-with-sizes" />

### Offset

Pass the `offset` prop to the `DrawerContent` to change the offset of the drawer
component.

<ExampleTabs name="drawer-with-offset" />

### Placement

Use the `placement` prop to change the placement of the drawer component.

<ExampleTabs name="drawer-with-placement" />

### Initial Focus

Use the `initialFocusEl` prop to set the initial focus of the drawer component.

<ExampleTabs name="drawer-with-initial-focus" />

## Props

### Root

<PropTable component="Drawer" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/editable.mdx

```text
---
title: Editable
description: Used for inline renaming of some text.
links:
  source: components/editable
  storybook: components-editable--basic
  recipe: editable
  ark: https://ark-ui.com/react/docs/components/editable
---

<ExampleTabs name="editable-basic" />

## Usage

```jsx
import { Editable } from "@chakra-ui/react"
```

```jsx
<Editable.Root>
  <Editable.Preview />
  <Editable.Input />
</Editable.Root>
```

## Examples

### Controlled

<ExampleTabs name="editable-controlled" />

### With Double Click

Use the `activationMode` prop to make the content editable when users double
click.

<ExampleTabs name="editable-with-double-click" />

### Disabled

Use the `disabled` prop to disable the editable component.

<ExampleTabs name="editable-disabled" />

### TextArea

You can make a text area editable.

<ExampleTabs name="editable-with-textarea" />

### With Controls

Add controls such as "edit", "cancel" and "submit" to `Editable` for better user
experience.

<ExampleTabs name="editable-with-controls" />

## Props

### Root

<PropTable component="Editable" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/em.mdx

```text
---
title: Em
description: Used to mark text for emphasis.
---

<ExampleTabs name="em-basic" />

## Usage

```js
import { Em } from "@chakra-ui/react"
```

```jsx
<Text>
  The <Em>design system</Em> is a collection of UI elements
</Text>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/empty-state.mdx

```text
---
title: Empty State
description: Used to indicate when a resource is empty or unavailable.
links:
  source: components/empty-state
  storybook: components-empty-state--basic
  recipe: empty-state
---

<ExampleTabs name="empty-state-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`empty-state` snippet

```sh
npx @chakra-ui/cli snippet add empty-state
```

The snippet includes a closed component composition for the `EmptyState`
component.

## Usage

```jsx
import { EmptyState } from "@/components/ui/empty-state"
```

```jsx
<EmptyState />
```

## Examples

### Action

Here's an example of an empty state with an action button.

<ExampleTabs name="empty-state-with-action" />

### List

Here's an example of an empty state with a list.

<ExampleTabs name="empty-state-with-list" />

## Props

### Root

<PropTable component="EmptyState" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/environment-provider.mdx

```text
---
title: Environment Provider
description: Used to render components in iframes, Shadow DOM, or Electron.
links:
  storybook: components-environment-provider--basic
  recipe: environment-provider
  ark: https://ark-ui.com/react/docs/utilities/environment-provider
---

We use
[Zag.js](https://zagjs.com/overview/composition#custom-window-environment)
internally, which relies on DOM query methods like `document.querySelectorAll`
and `document.getElementById`. In custom environments like iframes, Shadow DOM,
or Electron, these methods might not work as expected.

To handle this, Ark UI includes the `EnvironmentProvider`, allowing you to set
the appropriate root node or document, ensuring correct DOM queries.

## Usage

```jsx
import { EnvironmentProvider } from "@chakra-ui/react"
```

```jsx
<EnvironmentProvider>{/* Your App */}</EnvironmentProvider>
```

## Examples

### iframe

Here's an example that uses `react-frame-component` to set the
`EnvironmentProvider`'s value with the iframe environment.

```jsx
import { EnvironmentProvider } from "@chakra-ui/react"
import Frame, { FrameContextConsumer } from "react-frame-component"

export const Demo = () => (
  <Frame>
    <FrameContextConsumer>
      {({ document }) => (
        <EnvironmentProvider value={() => document}>
          {/* Your App */}
        </EnvironmentProvider>
      )}
    </FrameContextConsumer>
  </Frame>
)
```

### Shadow DOM

Here's an example that uses `react-shadow` to set the `EnvironmentProvider`'s
value with Shadow DOM environment.

```jsx
import { EnvironmentProvider } from "@chakra-ui/react"
import { useRef } from "react"
import root from "react-shadow"

export const Demo = () => {
  const portalRef = useRef()
  return (
    <root.div ref={portalRef}>
      <EnvironmentProvider
        value={() => portalRef?.current?.shadowRoot ?? document}
      >
        {/* Your App */}
      </EnvironmentProvider>
    </root.div>
  )
}
```

### Accessing Context

Use the `useEnvironmentContext` hook to access the `RootNode`, `Document`, and
`Window` context.

```jsx
import { useEnvironmentContext } from "@chakra-ui/react"

export const Demo = () => {
  const { getRootNode } = useEnvironmentContext()

  return <pre>{JSON.stringify(getRootNode(), null, 2)}</pre>
}
```

## Props

<PropTable component="Environment" part="EnvironmentProvider" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/field.mdx

```text
---
title: Field
description: Used to add labels, help text, and error messages to form fields.
links:
  source: components/field
  storybook: components-field--basic
  recipe: field
  ark: https://ark-ui.com/react/docs/components/field
---

<ExampleTabs name="field-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`field` snippet

```sh
npx @chakra-ui/cli snippet add field
```

The snippet includes a closed component composition for the `Field` component.

## Usage

```jsx
import { Field } from "@/components/ui/field"
```

```jsx
<Field>
  <Input />
</Field>
```

## Examples

### Error Text

Use the `invalid` and `errorText` to indicate that the field is invalid.

<ExampleTabs name="field-with-error-text" />

### Helper Text

Use the `helperText` prop to add helper text to the field.

<ExampleTabs name="field-with-helper-text" />

### Horizontal

Use the `orientation="horizontal"` prop to align the label and input
horizontally.

<ExampleTabs name="field-horizontal" />

### Disabled

Use the `disabled` prop to disable the field.

<ExampleTabs name="field-with-disabled" />

### Textarea

Here's how to use the field component with a textarea.

<ExampleTabs name="field-with-textarea" />

### Native Select

Here's how to use the field component with a native select.

<ExampleTabs name="field-with-native-select" />

### Required

Use the `required` prop to indicate that the field is required.

<ExampleTabs name="field-with-required" />

### Optional

Use the `optionalText` prop to indicate that the field is optional.

<ExampleTabs name="field-with-optional" />

## Props

### Root

<PropTable component="Field" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/fieldset.mdx

```text
---
title: Fieldset
description: A set of form controls optionally grouped under a common name.
links:
  source: components/fieldset
  storybook: components-fieldset--basic
  recipe: fieldset
  ark: https://ark-ui.com/react/docs/components/fieldset
---

<ExampleTabs name="fieldset-basic" />

## Usage

```jsx
import { Fieldset } from "@chakra-ui/react"
```

```jsx
<Fieldset.Root>
  <Fieldset.Legend />
  <Fieldset.Content />
</Fieldset.Root>
```

## Examples

### Disabled

Use the `disabled` prop to disable the fieldset to disable all input elements
within the fieldset.

<ExampleTabs name="fieldset-with-disabled" />

### Invalid

Use the `invalid` prop to mark the fieldset as invalid. This will show the error
text.

> Note: You need to pass the `invalid` prop to the `Field` component within the
> fieldset to make each input element invalid.

<ExampleTabs name="fieldset-with-invalid" />

## Props

<PropTable component="Fieldset" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/file-upload.mdx

```text
---
title: File Upload
description: Used to select and upload files from their device.
links:
  source: components/file-upload
  storybook: components-file-upload--basic
  recipe: file-upload
  ark: https://ark-ui.com/react/docs/components/file-upload
---

<ExampleTabs name="file-upload-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`file-upload` snippet

```sh
npx @chakra-ui/cli snippet add file-upload
```

The snippet includes a closed component composition for the `FileUpload`
component.

## Usage

```jsx
import {
  FileUploadList,
  FileUploadRoot,
  FileUploadTrigger,
} from "@/components/ui/file-upload"
```

```jsx
<FileUploadRoot>
  <FileUploadTrigger>
    <Button>
      <HiUpload /> Upload file
    </Button>
  </FileUploadTrigger>
  <FileUploadList />
</FileUploadRoot>
```

## Examples

### Directory

Use the `directory` prop to allow selecting a directory instead of a file.

<ExampleTabs name="file-upload-directory" />

### Media Capture

Use the `capture` prop to select and upload files from different environments
and media types.

> **Note:** This is
> [not fully supported](https://caniuse.com/mdn-api_htmlinputelement_capture) in
> all browsers.

<ExampleTabs name="file-upload-media-capture" />

### Multiple Files

Upload multiple files at once by using the `maxFiles` prop.

<ExampleTabs name="file-upload-multiple" />

### Dropzone

Drop multiple files inside the dropzone and use the `maxFiles` prop to set the
number of files that can be uploaded at once.

<ExampleTabs name="file-upload-with-dropzone" />

### Input

Use the `FileInput` component to create a trigger that looks like a text input.

<ExampleTabs name="file-upload-with-input" />

### Clearable

Here's an example of a clearable file upload input.

<ExampleTabs name="file-upload-with-input-clear" />

### Accepted Files

Define the accepted files for upload using the `accept` prop.

<ExampleTabs name="file-upload-accepted-files" />

## Props

### Root

<PropTable component="FileUpload" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/flex.mdx

```text
---
title: Flex
description: Used to manage flex layouts
links:
  source: components/flex
---

<ExampleTabs name="flex-basic" />

## Usage

```jsx
import { Flex } from "@chakra-ui/react"
```

```jsx
<Flex>
  <div />
  <div />
</Flex>
```

## Examples

### Direction

Use the `direction` or `flexDirection` prop to change the direction of the flex

<ExampleTabs name="flex-with-direction" />

### Align

Use the `align` or `alignItems` prop to align the children along the cross axis.

<ExampleTabs name="flex-with-align" />

### Justify

Use the `justify` or `justifyContent` prop to align the children along the main
axis.

<ExampleTabs name="flex-with-justify" />

### Order

Use the `order` prop to change the order of the children.

<ExampleTabs name="flex-with-order" />

### Auto Margin

Apply margin to a flex item to push it away from its siblings.

<ExampleTabs name="flex-with-auto-margin" />

### Wrap

Use the `wrap` or `flexWrap` prop to wrap the children when they overflow the
parent.

<ExampleTabs name="flex-with-wrap" />

## Props

<PropTable component="Flex" part="Flex" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/float.mdx

```text
---
title: Float
description: Used to anchor an element to the edge of a container.
links:
  source: components/float
  storybook: components-float--basic
---

<ExampleTabs name="float-basic" />

## Usage

Float requires a parent element with `position: relative` style applied.

```jsx
import { Box, Float } from "@chakra-ui/react"
```

```jsx
<Box position="relative">
  <Float>
    <div />
  </Float>
</Box>
```

## Examples

### Placement

Use the `placement` prop to position the element along the edges of the
container.

<ExampleTabs name="float-with-placements" />

### Offset X

Use the `offsetX` prop to offset the element along the x-axis.

<ExampleTabs name="float-with-offset-x" />

### Offset Y

Use the `offsetY` prop to offset the element along the y-axis.

<ExampleTabs name="float-with-offset-y" />

### Offset

Use the `offset` prop to offset the element along both axes.

<ExampleTabs name="float-with-offset" />

### Avatar

Here's an example of composing a `Float` component with an `Avatar` component.

<ExampleTabs name="float-with-avatar" />

## Props

<PropTable component="Float" part="Float" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/for.mdx

```text
---
title: For
description: Used to loop over an array and render a component for each item.
links:
  source: components/for
  storybook: components-for--basic
---

<ExampleTabs name="for-basic" />

## Usage

The `For` component is used to render a list of items in a strongly typed
manner. It is similar to the `.map()`.

```jsx
import { For } from "@chakra-ui/react"
```

```jsx
<For each={[]} fallback={...} />
```

## Examples

### Object

Here's an example of using the `For` component to loop over an object.

<ExampleTabs name="for-with-object" />

### Fallback

Use the `fallback` prop to render a fallback component when the array is empty
or undefined.

<ExampleTabs name="for-with-fallback" />

## Props

<PropTable component="For" part="For" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/format-byte.mdx

```text
---
title: Format Byte
description: Used to format bytes to a human-readable format
links:
  storybook: components-format-byte--basic
  ark: https://ark-ui.com/react/docs/utilities/format-byte
---

<ExampleTabs name="format-byte-basic" />

## Usage

```jsx
import { FormatByte } from "@chakra-ui/react"
```

```jsx
<FormatByte value={1000} />
```

## Examples

### Sizes

The format functions works for any size of bytes.

<ExampleTabs name="format-byte-sizes" />

### Format Bits

Use the `unit` prop to change the byte format to bits.

<ExampleTabs name="format-byte-with-unit" />

### Locale

Wrap the `FormatByte` component within the `LocaleProvider` to change the
locale.

<ExampleTabs name="format-byte-with-locale" />

### Unit Display

Use the `unitDisplay` prop to change the byte format to compact notation.

<ExampleTabs name="format-byte-with-unit-display" />

## Props

<PropTable component="Format" part="Byte" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/format-number.mdx

```text
---
title: Format Number
description: Used to format numbers to a specific locale and options
links:
  storybook: components-format-number--basic
  ark: https://ark-ui.com/react/docs/utilities/format-number
---

<ExampleTabs name="format-number-basic" />

## Usage

The number formatting logic is handled by the native `Intl.NumberFormat` API and
smartly cached to avoid performance issues when using the same locale and
options.

```jsx
import { FormatNumber } from "@chakra-ui/react"
```

```jsx
<FormatNumber value={1000} />
```

## Examples

### Percentage

Use the `style=percentage` prop to change the number format to percentage.

<ExampleTabs name="format-number-with-percentage" />

### Currency

Use the `style=currency` prop to change the number format to currency.

<ExampleTabs name="format-number-with-currency" />

### Locale

Wrap the `FormatNumber` component within the `LocaleProvider` to change the
locale.

<ExampleTabs name="format-number-with-locale" />

### Unit

Use the `style=unit` prop to change the number format to unit.

<ExampleTabs name="format-number-with-unit" />

### Compact Notation

Use the `notation=compact` prop to change the number format to compact notation.

<ExampleTabs name="format-number-with-compact" />

## Props

The `FormatNumber` component supports all `Intl.NumberFormat` options in
addition to the following props:

<PropTable component="Format" part="Number" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/grid.mdx

```text
---
title: Grid
description: Used to manage grid layouts
links:
  source: components/grid
  storybook: components-grid--basic
---

<ExampleTabs name="grid-basic" />

## Usage

```jsx
import { Grid, GridItem } from "@chakra-ui/react"
```

```jsx
<Grid>
  <GridItem />
  <GridItem />
</Grid>
```

## Examples

### Col Span

Pass `colSpan` prop to `GridItem` to span across columns.

<ExampleTabs name="grid-with-col-span" />

### Spanning Columns

In some layouts, you may need certain grid items to span specific amount of
columns or rows instead of an even distribution

<ExampleTabs name="grid-spanning-columns" />

## Props

<PropTable component="Grid" part="Grid" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/group.mdx

```text
---
title: Group
description: Used to group and attach elements together
links:
  source: components/group
  storybook: components-group--basic
---

<ExampleTabs name="group-basic" />

## Usage

```jsx
import { Group } from "@chakra-ui/react"
```

```jsx
<Group>
  <div />
  <div />
</Group>
```

## Examples

### Button

Here's an example of using the `Group` component to group buttons together.

<ExampleTabs name="group-with-button" />

### Attached

Use the `attached` prop to attach the children together.

<ExampleTabs name="group-with-attached" />

### Grow

Use the `grow` prop to make the children grow to fill the available space.

<ExampleTabs name="group-with-grow" />

## Props

<PropTable component="Group" part="Group" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/heading.mdx

```text
---
title: Heading
description: Used to render semantic HTML heading elements.
---

<ExampleTabs name="heading-basic" />

## Usage

```js
import { Heading } from "@chakra-ui/react"
```

```jsx
<Heading>I'm a Heading</Heading>
```

## Examples

### Sizes

Use the `size` prop to change the size of the heading component.

<ExampleTabs name="heading-with-sizes" />

### Highlight

Compose the `Heading` component with the `Highlight` component to highlight
text.

<ExampleTabs name="heading-with-highlight" />

### As another element

Use the `as` prop to render the heading as another HTML element.

<ExampleTabs name="heading-with-as-prop" />

### Weights

Use the `fontWeight` prop to change the weight of the heading component.

<ExampleTabs name="heading-with-weights" />

### Composition

Use the `Heading` component to compose other components.

<ExampleTabs name="heading-with-composition" />

## Props

<PropTable component="Heading" part="Heading" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/highlight.mdx

```text
---
title: Highlight
description: Used to highlight substrings of a text.
links:
  source: components/highlight
  storybook: typography-highlight--basic
  recipe: highlight
  ark: https://ark-ui.com/react/docs/utilities/highlight
---

<ExampleTabs name="highlight-basic" />

## Usage

```jsx
import { Highlight } from "@chakra-ui/react"
```

```jsx
<Highlight query="Hello">Hello World</Highlight>
```

## Examples

### Multiple

Pass an array of strings to the `query` prop to highlight multiple substrings.

<ExampleTabs name="highlight-multiple" />

### Custom Style

Use the `styles` prop to customize the style of the highlighted text.

<ExampleTabs name="highlight-with-custom-style" />

### Search Query

Use the highlight component to highlight search query results.

<ExampleTabs name="highlight-search-query" />

### With Squiggle

Here's an example of how to render a custom squiggle image around the
highlighted text. Useful for a more fancy effect.

<ExampleTabs name="highlight-with-squiggle" />

## Props

<PropTable component="Highlight" part="Highlight" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/hover-card.mdx

```text
---
title: Hover Card
description: Used to display content when hovering over an element
links:
  source: components/hover-card
  storybook: components-hover-card--basic
  recipe: hover-card
  ark: https://ark-ui.com/react/docs/components/hover-card
---

<ExampleTabs name="hover-card-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`hover-card` snippet

```sh
npx @chakra-ui/cli snippet add hover-card
```

The snippet includes component compositions based on the `HoverCard` component.

## Usage

```jsx
import {
  HoverCardArrow,
  HoverCardContent,
  HoverCardRoot,
  HoverCardTrigger,
} from "@/components/ui/hover-card"
```

```jsx
<HoverCardRoot>
  <HoverCardTrigger />
  <HoverCardContent>
    <HoverCardArrow />
  </HoverCardContent>
</HoverCardRoot>
```

## Examples

### Controlled

Use the `open` and `onOpenChange` to control the visibility of the hover card.

<ExampleTabs name="hover-card-controlled" />

### Delays

Control the open and close delays using the `openDelay` and `closeDelay` props.

<ExampleTabs name="hover-card-with-delay" />

### Placement

Use the `positioning.placement` prop to configure the underlying `floating-ui`
positioning logic.

<ExampleTabs name="hover-card-with-placement" />

## Accessibility

HoverCard should be used solely for supplementary information that is not
essential for understanding the context.

It is inaccessible to screen readers and cannot be activated via keyboard, so
avoid placing crucial content within it.

## Props

<PropTable component="HoverCard" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/icon-button.mdx

```text
---
title: Icon Button
description: Used to render an icon within a button
links:
  source: components/button
  storybook: components-icon-button--basic
  recipe: button
---

<ExampleTabs name="icon-button-basic" />

## Usage

```jsx
import { IconButton } from "@chakra-ui/react"
```

```jsx
<IconButton aria-label="Search database">
  <LuSearch />
</IconButton>
```

## Examples

### Sizes

Use the `size` prop to change the size of the button.

<ExampleTabs name="icon-button-with-sizes" />

### Variants

Use the `variant` prop to change its visual style

<ExampleTabs name="icon-button-with-variants" />

### Color

Use the `colorPalette` prop to change the color of the button

<ExampleTabs name="icon-button-with-colors" />

### Rounded

Set `rounded="full"` to make the button fully rounded

<ExampleTabs name="icon-button-rounded" />

## Props

<PropTable component="Button" part="Button" omit={["loading", "loadingText"]} />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/icon.mdx

```text
---
title: Icon
description: Used to display an svg icon
links:
  source: components/icon
  storybook: components-icon--basic
---

<ExampleTabs name="icon-basic" />

## Usage

```jsx
import { Icon } from "@chakra-ui/react"
```

```jsx
<Icon />
```

:::warning

Chakra doesn't provide any icons out of the box. Use popular icon libraries like
[react-icons](https://react-icons.github.io/react-icons/) or
[lucide-react](https://lucide.dev/react/)

:::

## Examples

### React Icons

Integrate with popular react icon libraries like `react-icons`

<ExampleTabs name="icon-with-react-icon" />

### Custom svg

Use the `asChild` prop to render custom svg icons within the `Icon` component

<ExampleTabs name="icon-with-custom-svg" />

### Create Icon

Use the `createIcon` utility to create custom icons

<ExampleTabs name="icon-with-create-icon" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/image.mdx

```text
---
title: Image
description: Used to display images
---

<ExampleTabs name="image-basic" />

## Usage

```js
import { Image } from "@chakra-ui/react"
```

```jsx
<Image src="..." />
```

## Examples

### Height

Use the `height` prop to change the height of the image component.

<ExampleTabs name="image-with-height" />

### Circular

Here's an example of how to render a circular image.

<ExampleTabs name="image-circular" />

### Aspect Ratio

Use the `aspectRatio` prop to change the aspect ratio of the image component.

<ExampleTabs name="image-with-aspect-ratio" />

### Fit

By default, the image applies `object-fit: cover`. Use the `fit` prop to change
the fit of the image component.

<ExampleTabs name="image-with-fit" />

### Next.js Image

Use the `asChild` prop to render the image as a child of the `Image` component.

```jsx
// import NextImage from "next/image"

<Image asChild>
  <NextImage src="..." alt="..." />
</Image>
```

## Props

The `Image` component supports all native props for the `img` element.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/input.mdx

```text
---
title: Input
description: Used to get user input in a text field.
links:
  source: components/input
  storybook: components-input--basic
  recipe: input
---

<ExampleTabs name="input-basic" />

## Usage

```jsx
import { Input } from "@chakra-ui/react"
```

```jsx
<Input placeholder="..." />
```

## Examples

### Variants

Use the `variant` prop to change the visual style of the input.

<ExampleTabs name="input-with-variants" />

### Sizes

Use the `size` prop to change the size of the input.

<ExampleTabs name="input-with-sizes" />

### Helper Text

Pair the input with the `Field` component to add helper text.

<ExampleTabs name="input-with-helper-text" />

### Error Text

Pair the input with the `Field` component to add error text.

<ExampleTabs name="input-with-error-text" />

### Field

Compose the input with the `Field` component to add a label, helper text, and
error text.

<ExampleTabs name="input-with-field" />

### Hook Form

Here's an example of how to integrate the input with `react-hook-form`.

<ExampleTabs name="input-with-hook-form" />

### Left and Right Element

Pair the input with the `InputElement` component to add an element to the left
or right of the input.

<ExampleTabs name="input-with-left-element" />

<ExampleTabs name="input-with-left-and-right-element" />

### Addon

Use the `InputAddon` and `Group` components to add an addon to the input.

<ExampleTabs name="input-with-addon" />

### Disabled

Use the `disabled` prop to disable the input.

<ExampleTabs name="input-with-disabled" />

### Placeholder Style

Use the `_placeholder` prop to style the placeholder text.

<ExampleTabs name="input-with-placeholder-style" />

### Floating Label

Here's an example of how to build a floating label to the input.

<ExampleTabs name="input-with-floating-label" />

### Mask

Here's an example of using the `use-mask-input` library to mask the input shape.

<ExampleTabs name="input-with-mask" />

## Props

<PropTable component="Input" part="Input" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/kbd.mdx

```text
---
title: Kbd
description: Used to show key combinations for an action
links:
  source: components/kbd
  storybook: components-kbd--basic
  recipe: kbd
---

<ExampleTabs name="kbd-basic" />

## Usage

```jsx
import { Kbd } from "@chakra-ui/react"
```

```jsx
<Kbd>F12</Kbd>
```

## Examples

### Combinations

Render `Kbd` to showcase key combinations

<ExampleTabs name="kbd-with-combinations" />

### Function Keys

Here's an example of using `Kbd` to showcase function keys

<ExampleTabs name="kbd-function-keys" />

### Variants

Use the `variant` prop to change the appearance of the `Kbd` component

<ExampleTabs name="kbd-with-variants" />

### Sizes

Use the `size` prop to change the size of the `Kbd` component

<ExampleTabs name="kbd-with-sizes" />

### Within Text

Use `Kbd` within text to highlight key combinations

<ExampleTabs name="kbd-within-text" />

## Props

<PropTable component="Kbd" part="Kbd" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/link-overlay.mdx

```text
---
title: Link Overlay
description: Used to stretch a link over a container.
---

<ExampleTabs name="link-overlay-basic" />

## Usage

The `LinkOverlay` component provides a semantic way to link an entire component
or card.

```jsx
import { LinkBox, LinkOverlay } from "@chakra-ui/react"
```

```jsx
<LinkBox>
  <LinkOverlay />
</LinkBox>
```

## Examples

### Article

Here's an example of using `LinkOverlay` to link an entire article.

<ExampleTabs name="link-overlay-article" />

### Custom Link

Use the `asChild` prop to add support for custom Link component like `next/link`
or react router's `Link`

```jsx
import { LinkBox, LinkOverlay } from "@chakra-ui/react"
import NextLink from "next/link"

function Example() {
  return (
    <LinkBox as="article">
      <h2>
        <LinkOverlay asChild>
          <NextLink href="#">Blog Post Title</NextLink>
        </LinkOverlay>
      </h2>
      <p>Some blog post content</p>
      <NextLink href="#inner-link">Some inner link</NextLink>
    </LinkBox>
  )
}
```

## Caveat

One of the side-effects of this technique is that the content can't be
"selectable" (i.e. with a pointing device), however, this seems to be pretty
uncommon in web design.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/link.mdx

```text
---
title: Link
description: Used to provide accessible navigation
links:
  source: components/link
  storybook: typography-link--basic
  recipe: link
---

<ExampleTabs name="link-basic" />

## Usage

```jsx
import { Link } from "@chakra-ui/react"
```

```jsx
<Link href="...">Click here</Link>
```

## Examples

### Variants

Use the `variant` prop to change the appearance of the `Link` component

<ExampleTabs name="link-with-variants" />

### Within Text

Use `Link` within a text to create a hyperlink

<ExampleTabs name="link-within-text" />

### External

Add an external link icon to the `Link` component

<ExampleTabs name="link-with-external" />

### Routing Library

Use the `asChild` prop to compose `Link` with framework links like (Next.js)

```jsx
import { Link as ChakraLink } from "@chakra-ui/react"
import NextLink from "next/link"

const Demo = () => {
  return (
    <ChakraLink asChild>
      <NextLink href="/about">Click here</NextLink>
    </ChakraLink>
  )
}
```

## Props

<PropTable component="Link" part="Link" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/list.mdx

```text
---
title: List
description: Used to display a list of items
links:
  source: components/list
  storybook: components-list--basic
  recipe: list
---

<ExampleTabs name="list-basic" />

## Usage

```jsx
import { List } from "@chakra-ui/react"
```

```jsx
<List.Root>
  <List.Item>Item 1</List.Item>
  <List.Item>Item 2</List.Item>
</List.Root>
```

## Examples

### Ordered

Pass the `as="ol"` prop to create an ordered list

<ExampleTabs name="list-ordered" />

### Icon

Use the `List.Indicator` component to add an icon to the list

<ExampleTabs name="list-with-icon" />

### Nested

Here's an example of a nested list

<ExampleTabs name="list-nested" />

### Marker Style

Use the `_marker` prop to style the marker of the list

<ExampleTabs name="list-with-marker-style" />

## Props

<PropTable component="List" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/locale-provider.mdx

```text
---
title: Locale Provider
description: Used for globally setting the locale
---

## Usage

The `LocaleProvider` component sets the locale for your app, formatting dates,
numbers, and other locale-specific data.

> Most Chakra UI components that read the locale set by the `LocaleProvider`.

```jsx
import { LocaleProvider, useLocaleContext } from "@chakra-ui/react"
```

```jsx
<LocaleProvider locale="...">{/* Your App */}</LocaleProvider>
```

## Examples

### Setting Locale

Set the `locale` prop to the locale you want to use.

```jsx
<LocaleProvider locale="ar-BH">
  <Component />
</LocaleProvider>
```

### Reading Locale

```jsx
export const Usage = () => {
  const { locale, dir } = useLocaleContext()
  return <pre>{JSON.stringify({ locale, dir }, null, 2)}</pre>
}
```

## Props

<PropTable component="Locale" part="LocaleProvider" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/mark.mdx

```text
---
title: Mark
description: Used to mark text for emphasis.
---

<ExampleTabs name="mark-basic" />

## Usage

```js
import { Mark } from "@chakra-ui/react"
```

```jsx
<Text>
  The <Mark>design system</Mark> is a collection of UI elements
</Text>
```

## Examples

### Variants

Use the `variant` prop to change the color of the mark.

<Example name="mark-with-variants" />

## Props

<PropTable component="Mark" part="Mark" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/menu.mdx

```text
---
title: Menu
description: Used to create an accessible dropdown menu
links:
  source: components/menu
  storybook: components-menu--basic
  recipe: menu
  ark: https://ark-ui.com/react/docs/components/menu
---

<ExampleTabs name="menu-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`menu` snippet

```sh
npx @chakra-ui/cli snippet add menu
```

The snippet includes a closed component composition for the `Menu` component.

## Usage

```jsx
import {
  MenuContent,
  MenuItem,
  MenuRoot,
  MenuTrigger,
} from "@/components/ui/menu"
```

```jsx
<MenuRoot>
  <MenuTrigger />
  <MenuContent>
    <MenuItem value="..." />
  </MenuContent>
</MenuRoot>
```

## Examples

### Command

Use the `MenuItemCommand` component to display a command in the menu.

<ExampleTabs name="menu-with-command" />

### Context menu

Use the `MenuContextTrigger` component to create a context menu.

<ExampleTabs name="menu-with-context-trigger" />

### Group

Use the `MenuItemGroup` component to group related menu items.

<ExampleTabs name="menu-with-group" />

### Danger Item

Here's an example of how to style a menu item that is used to delete an item.

<ExampleTabs name="menu-with-danger-item" />

### Submenu

Here's an example of how to create a submenu.

<ExampleTabs name="menu-with-submenu" />

### Links

Pass the `asChild` prop to the `MenuItem` component to render a link.

<ExampleTabs name="menu-with-links" />

### With Radio

Here's an example of how to create a menu with radio.

<ExampleTabs name="menu-with-radio-items" />

### Icon and Command

Compose the menu to include icons and commands.

<ExampleTabs name="menu-with-icon-and-command" />

### Placement

Use the `positioning.placement` prop to control the placement of the menu.

<ExampleTabs name="menu-with-placement" />

### Mixed Layout

Here's an example of how to create a mixed layout of menu items. In this layout,
the top horizontal menu includes common menu items.

<ExampleTabs name="menu-with-mixed-layout" />

## Props

### Root

<PropTable component="Menu" part="Root" />

### Item

<PropTable component="Menu" part="Item" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/native-select.mdx

```text
---
title: Select (Native)
description: Used to pick a value from predefined options.
links:
  source: components/native-select
  storybook: components-native-select--basic
  recipe: native-select
---

<ExampleTabs name="native-select-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`native-select` snippet

```sh
npx @chakra-ui/cli snippet add native-select
```

The snippet includes a closed component composition for the `NativeSelect`
component.

## Usage

```jsx
import {
  NativeSelectField,
  NativeSelectRoot,
} from "@/components/ui/native-select"
```

```jsx
<NativeSelectRoot>
  <NativeSelectField>
    <option value="1">Option 1</option>
    <option value="2">Option 2</option>
  </NativeSelectField>
</NativeSelectRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the select component.

<ExampleTabs name="native-select-with-sizes" />

### Variants

Use the `variant` prop to change the appearance of the select component.

<ExampleTabs name="native-select-with-variants" />

### Items Prop

Pass the `items` prop to the `NativeSelectField` component to render a list of
options.

<ExampleTabs name="native-select-with-items" />

### Controlled

Use the `value` and `onChange` props to control the select component.

<ExampleTabs name="native-select-controlled" />

### Hook Form

Here is an example of how to use the `NativeSelect` component with
`react-hook-form`.

<ExampleTabs name="native-select-with-hook-form" />

## Props

### Root

<PropTable component="NativeSelect" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/number-input.mdx

```text
---
title: Number Input
description:
  Used to enter a number, and increment or decrement the value using stepper
  buttons.
links:
  source: components/number-input
  storybook: components-numberinput--basic
  recipe: number-input
  ark: https://ark-ui.com/react/docs/components/number-input
---

<ExampleTabs name="number-input-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`number-input` snippet

```sh
npx @chakra-ui/cli snippet add number-input
```

The snippet includes a closed component composition for the `NumberInput`
component.

## Usage

```jsx
import {
  NumberInputField,
  NumberInputLabel,
  NumberInputRoot,
} from "@/components/ui/number-input"
```

```jsx
<NumberInputRoot>
  <NumberInputLabel />
  <NumberInputField />
</NumberInputRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the number input.

<ExampleTabs name="number-input-with-sizes" />

### Formatting

Use the `formatOptions` prop to format the number input value. The value of this
maps to `Intl.NumberFormatOptions` and is applied based on the current locale.

<ExampleTabs name="number-input-with-format-options" />

### Min and Max

Use the `min` and `max` props to set the minimum and maximum values of the
number input.

If value entered is less than `min` or greater than `max`, the value will be
clamped to the nearest boundary on blur, or enter key press.

<ExampleTabs name="number-input-with-min-max" />

### Step

Use the `step` prop to change the increment or decrement interval of the number
input.

<ExampleTabs name="number-input-with-step" />

### Controlled

Use the `value` and `onValueChange` props to control the value of the number
input.

<ExampleTabs name="number-input-controlled" />

### Mobile Stepper

Use the `StepperInput` component to create a stepper input for mobile devices.

> You can also apply `spinOnPress: false` to disable the spin functionality.

<ExampleTabs name="number-input-with-stepper" />

### Mouse Wheel

Use the `allowMouseWheel` prop to enable or disable the mouse wheel to change

<ExampleTabs name="number-input-with-mouse-wheel" />

### Disabled

Use the `disabled` prop to disable the number input.

<ExampleTabs name="number-input-with-disabled" />

### Invalid

Use the `Field` component and pass the `invalid` prop to indicate that the
number input is invalid.

<ExampleTabs name="number-input-with-invalid" />

### Helper Text

Use the `Field` component and pass the `helperText` prop to add helper text to
the number input.

<ExampleTabs name="number-input-with-field" />

### Hook Form

Here is an example of how to use the `NumberInput` component with
`react-hook-form`.

<ExampleTabs name="number-input-with-hook-form" />

## Props

### Root

<PropTable component="NumberInput" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/pagination.mdx

```text
---
title: Pagination
description: Used to navigate through a series of pages.
links:
  source: components/pagination
  storybook: components-pagination--basic
  ark: https://ark-ui.com/react/docs/components/pagination
---

<ExampleTabs name="pagination-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`pagination` snippet

```sh
npx @chakra-ui/cli snippet add pagination
```

The snippet includes a closed component composition for the `Pagination`
component.

## Usage

```jsx
import {
  PaginationItems,
  PaginationNextTrigger,
  PaginationPageText,
  PaginationPrevTrigger,
  PaginationRoot,
} from "@/components/ui/pagination"
```

```jsx
<PaginationRoot>
  <PaginationPrevTrigger />
  <PaginationItems />
  <PaginationPageText />
  <PaginationNextTrigger />
</PaginationRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the pagination.

:::info

The pagination sizes are mapped to the `Button` component sizes.

:::

<ExampleTabs name="pagination-with-sizes" />

### Variants

Use the `variant` prop to control the variant of the pagination items and
ellipsis.

> The variant matches the `Button` component variant.

<ExampleTabs name="pagination-with-variants" />

### Controlled

Use the `page` and `onPageChange` props to control the current page.

<ExampleTabs name="pagination-controlled" />

### Sibling Count

Use `siblingCount` to control the number of sibling pages to show before and
after the current page.

<ExampleTabs name="pagination-with-sibling-count" />

### Compact

Use the `PaginationPageText` to create a compact pagination. This can be useful
for mobile views.

<ExampleTabs name="pagination-compact" />

### As Link

Use the `getHref` prop to create a pagination that navigates via links. This
will use the `LinkButton` component to create the links.

:::info

Edit the `LinkButton` component to point to the correct framework link component
if needed.

:::

<ExampleTabs name="pagination-as-link" />

### Attached

Here's an example of composing the pagination with the `Group` component to
attach the pagination items and triggers.

<ExampleTabs name="pagination-attached" />

### Count Text

Pass `format=long` to the `PaginationPageText` component to show the count text

<ExampleTabs name="pagination-with-count-text" />

### Data Driven

Here's an example of controlling the pagination state and using the state to
chunk the data.

<ExampleTabs name="pagination-with-content" />

## Props

### Root

<PropTable component="Pagination" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/password-input.mdx

```text
---
title: Password Input
description: Used to collect passwords.
composition: true
---

<ExampleTabs name="password-input-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`password-input` snippet

```sh
npx @chakra-ui/cli snippet add password-input
```

The snippet includes a closed component composition for the `PasswordInput`
component.

## Usage

```jsx
import {
  PasswordInput,
  PasswordStrengthMeter,
} from "@/components/ui/password-input"
```

```jsx
<PasswordInput />
<PasswordStrengthMeter />
```

## Examples

### Sizes

Use the `size` prop to change the size of the password input.

:::info

The password input sizes are mapped to the `Input` component sizes.

:::

<ExampleTabs name="password-input-with-sizes" />

### Controlled

Use the `value` and `onChange` props to control the current page.

<ExampleTabs name="password-input-controlled" />

### Hook Form

Here's an example of how to use the `PasswordInput` component with
`react-hook-form`.

<ExampleTabs name="password-input-with-hook-form" />

### Controlled Visibility

Use the `visible` and `onVisibleChange` props to control the visibility of the
password input.

<ExampleTabs name="password-input-controlled-visibility" />

### Strength Indicator

Render the `PasswordStrengthMeter` component to show the strength of the
password. Compute the `value` prop based on the password input `value`.

<ExampleTabs name="password-input-with-strength-indicator" />

## Props

### Root

<PropTable component="PasswordInput" part="PasswordInput" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/pin-input.mdx

```text
---
title: Pin Input
description: Used to capture a pin code or otp from the user
links:
  source: components/pin-input
  storybook: components-pin-input--basic
  recipe: pin-input
  ark: https://ark-ui.com/react/docs/components/pin-input
---

<ExampleTabs name="pin-input-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`pin-input` snippet

```sh
npx @chakra-ui/cli snippet add pin-input
```

The snippet includes a closed component composition for the `PinInput`
component.

## Usage

```jsx
import { PinInput } from "@/components/ui/pin-input"
```

```jsx
<PinInput />
```

## Examples

### Sizes

Use the `size` prop to change the size of the pin input component.

<ExampleTabs name="pin-input-with-sizes" />

### One time code

Use the `otp` prop to make the pin input component behave like a one-time code
input. This helps improve the user experience when entering OTP codes.

<ExampleTabs name="pin-input-with-otp" />

### Mask

Use the `mask` prop to obscure the entered pin code.

<ExampleTabs name="pin-input-with-mask" />

### Placeholder

Use the `placeholder` prop to add a placeholder to the pin input component.

<ExampleTabs name="pin-input-with-placeholder" />

### Field

Here's an example of how to compose the `Field` and the `PinInput` components

<ExampleTabs name="pin-input-with-field" />

### Hook Form

Here's an example of how to compose the `Field` and the `PinInput` components
with `react-hook-form`

<ExampleTabs name="pin-input-with-hook-form" />

### Controlled

Use the `value` and `onValueChange` props to control the value of the pin input

<ExampleTabs name="pin-input-controlled" />

### Attached

Use the `attached` prop to attach the pin input to the input field

<ExampleTabs name="pin-input-attached" />

### Alphanumeric

Use the `type` prop to allow the user to enter alphanumeric characters. Values
can be either `alphanumeric`, `numeric`, or `alphabetic`

<ExampleTabs name="pin-input-alphanumeric" />

### Without Snippet

If you don't want to use the snippet, you can use the `PinInput` component from
the `@chakra-ui/react` package.

<ExampleTabs name="pin-input-without-snippet" />

## Props

### Root

<PropTable component="PinInput" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/popover.mdx

```text
---
title: Popover
description: Used to show detailed information inside a pop-up
links:
  source: components/popover
  storybook: components-popover--basic
  recipe: popover
  ark: https://ark-ui.com/react/docs/components/popover
---

<ExampleTabs name="popover-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`popover` snippet

```sh
npx @chakra-ui/cli snippet add popover
```

The snippet includes component compositions based on the `Popover` component.

## Usage

```jsx
import {
  PopoverBody,
  PopoverContent,
  PopoverRoot,
  PopoverTitle,
  PopoverTrigger,
} from "@/components/ui/popover"
```

```jsx
<PopoverRoot>
  <PopoverTrigger />
  <PopoverContent>
    <PopoverBody>
      <PopoverTitle />
    </PopoverBody>
  </PopoverContent>
</PopoverRoot>
```

## Examples

### Controlled

Use the `open` and `onOpenChange` to control the visibility of the popover.

<ExampleTabs name="popover-controlled" />

### Sizes

Use the `size` prop to change the size of the popover component.

<ExampleTabs name="popover-with-sizes" />

### Lazy Mount

Use the `lazyMounted` and/or `unmountOnExit` prop to defer the mounting of the
popover content until it's opened.

<ExampleTabs name="popover-lazy-mounted" />

### Placement

Use the `positioning.placement` prop to configure the underlying `floating-ui`
positioning logic.

<ExampleTabs name="popover-with-placement" />

### Offset

Use the `positioning.offset` prop to adjust the position of the popover content.

<ExampleTabs name="popover-with-offset" />

### Same Width

Use the `positioning.sameWidth` prop to make the popover content the same width
as the trigger.

<ExampleTabs name="popover-with-same-width" />

### Nested Popover

When nesting overlay elements like popover, select, menu, inside of the popover,
set `portalled=false` on them.

Here's an example of a popover inside another popover.

<ExampleTabs name="popover-nested" />

### Initial Focus

Use the `initialFocusEl` prop to set the initial focus of the popover content.

<ExampleTabs name="popover-with-initial-focus" />

### Form

Here's an example of a popover with a form inside.

<ExampleTabs name="popover-with-form" />

### Custom Background

Use the `--popover-bg` CSS variable to change the background color of the
popover content and its arrow.

<ExampleTabs name="popover-with-custom-bg" />

### Without Snippet

If you don't want to use the snippet, you can use the `Popover` component from
the `@chakra-ui/react` package.

<ExampleTabs name="popover-without-snippet" />

## Props

### Root

<PropTable component="Popover" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/portal.mdx

```text
---
title: Portal
description: Used to render an element outside the DOM hierarchy.
---

## Usage

The `Portal` uses the `ReactDOM.createPortal` API to render an element at the
end of `document.body` or specific container.

```jsx
import { Portal } from "@chakra-ui/react"
```

```jsx
<Portal>
  <div>Portal content</div>
</Portal>
```

## Examples

### Custom Container

Use the `container` prop to render the portal in a custom container.

```jsx
import { Portal } from "@chakra-ui/react"

const Demo = () => {
  const containerRef = React.useRef()
  return (
    <>
      <Portal container={containerRef}>
        <div>Portal content</div>
      </Portal>
      <div ref={containerRef} />
    </>
  )
}
```

### Disabled

Use the `disabled` prop to disable the portal. This will render the children in
the same DOM hierarchy.

```jsx
import { Portal } from "@chakra-ui/react"

const Demo = () => {
  return (
    <Portal disabled>
      <div>Will render the content in place</div>
    </Portal>
  )
}
```

## Server Rendering

During SSR, the `Portal` component directly renders its content. If you run into
any mismatch warnings, we recommended conditionally rendering the `Portal`
component only on the client-side.

## Props

<PropTable component="Portal" part="Portal" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/progress-circle.mdx

```text
---
title: Progress Circle
description: Used to display a task's progress in a circular form.
links:
  source: components/progress-circle
  storybook: components-progress-circle--basic
  recipe: progress-circle
  ark: https://ark-ui.com/react/docs/components/progress-circular
---

<ExampleTabs name="progress-circle-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`progress-circle` snippet

```sh
npx @chakra-ui/cli snippet add progress-circle
```

The snippet includes a closed component composition for the `ProgressCircle`
component.

## Usage

```jsx
import {
  ProgressCircleRing,
  ProgressCircleRoot,
} from "@/components/ui/progress-circle"
```

```jsx
<ProgressCircleRoot>
  <ProgressCircleRing />
  <ProgressCircleValueText />
</ProgressCircleRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the progress circle component.

<ExampleTabs name="progress-circle-with-sizes" />

### Colors

Use the `colorPalette` prop to change the color scheme of the component.

<ExampleTabs name="progress-circle-with-colors" />

### Value Text

Render the `ProgressCircleValueText` component to display the progress value.

<ExampleTabs name="progress-circle-with-value-text" />

### Custom Thickness

Pass the `--thickness` css variable to the `ProgressCircleRing` component to
change the thickness of the ring.

<ExampleTabs name="progress-circle-with-thickness" />

### Indeterminate

Set the `value` prop to `null` to render the indeterminate state.

<ExampleTabs name="progress-circle-indeterminate" />

## Props

### Root

<PropTable component="ProgressCircle" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/progress.mdx

```text
---
title: Progress
description: Used to display the progress status for a task.
links:
  source: components/progress
  storybook: components-progress--basic
  recipe: progress
  ark: https://ark-ui.com/react/docs/components/progress-linear
---

<ExampleTabs name="progress-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`progress` snippet

```sh
npx @chakra-ui/cli snippet add progress
```

The snippet includes a closed component composition for the `Progress`
component.

## Usage

```jsx
import { ProgressBar, ProgressRoot } from "@/components/ui/progress"
```

```jsx
<ProgressRoot>
  <ProgressLabel />
  <ProgressValueText />
  <ProgressBar />
</ProgressRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the progress bar.

<ExampleTabs name="progress-with-sizes" />

### Variants

Use the `variant` prop to change the visual style of the progress bar.

<ExampleTabs name="progress-with-variants" />

### Colors

Use the `colorPalette` prop to change the color of the progress bar.

<ExampleTabs name="progress-with-colors" />

### Inline Label

Compose the `ProgressLabel` and `ProgressValueText` components to create an
inline label for the progress bar.

<ExampleTabs name="progress-with-inline-label" />

### Info tip

Use the `info` prop to add a tooltip to the progress bar.

<ExampleTabs name="progress-with-label-info" />

### Indeterminate

Set the value to `null` to show an indeterminate progress bar.

<ExampleTabs name="progress-indeterminate" />

### Stripes

Set the `striped` prop to `true` to add stripes to the progress bar.

<ExampleTabs name="progress-with-stripes" />

### Animated Stripes

Set the `animated` prop to `true` to animate the stripes.

<ExampleTabs name="progress-with-animated-stripes" />

## Props

### Root

<PropTable component="Progress" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/prose.mdx

```text
---
title: Prose
description: Used to style remote HTML content
composition: true
---

<ExampleTabs name="prose-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`prose` snippet

```sh
npx @chakra-ui/cli snippet add prose
```

## Usage

```jsx
import { Prose } from "@/components/ui/prose"
```

```jsx
<Prose>
  <div dangerouslySetInnerHTML={{ __html: "..." }} />
</Prose>
```

## Examples

### Sizes

Use the `size` prop to change the size of the `Prose` component

<ExampleTabs name="prose-with-sizes" />

### Blockquote

Blockquote elements are styled to match the design language of the `Blockquote`
component.

<ExampleTabs name="prose-with-blockquote" />

### List

List elements are styled to match the design language of the `List` component.

<ExampleTabs name="prose-with-list" />

### React Markdown

Here's an example of using the `react-markdown` library to render markdown
content.

<ExampleTabs name="prose-with-react-markdown" />

### Table

The table elements are styled to match the design language of the `Table`
component.

<ExampleTabs name="prose-with-table" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/radio-card.mdx

```text
---
title: Radio Card
description: Used to select an option from list
links:
  source: components/radio-card
  storybook: components-radio-card--basic
  recipe: radio-card
  ark: https://ark-ui.com/react/docs/components/radio-group
---

<ExampleTabs name="radio-card-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`radio-card` snippet

```sh
npx @chakra-ui/cli snippet add radio-card
```

The snippet includes a closed component composition for the `RadioCard`
component.

## Usage

A RadioCard is a form element with a larger interaction surface represented as a
card.

```jsx
import {
  RadioCardItem,
  RadioCardLabel,
  RadioCardRoot,
} from "@/components/ui/radio-card"
```

```jsx
<RadioCardRoot>
  <RadioCardLabel />
  <RadioCardItem />
</RadioCardRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the radio card.

<ExampleTabs name="radio-card-with-sizes" />

### Colors

Use the `colorPalette` prop to change the color of the radio card.

<ExampleTabs name="radio-card-with-colors" />

### Icon

Here's an example of using a label, description and icon within the
`RadioCardItem`.

<ExampleTabs name="radio-card-with-icon" />

### No Indicator

Here's an example of setting the `indicator` prop to `null` to hide the
indicator.

<ExampleTabs name="radio-card-without-indicator" />

### No Indicator (Vertical)

Here's an example of a RadioCard with no indicator and content aligned
vertically.

<ExampleTabs name="radio-card-without-indicator-vertical" />

### Centered

Here's an example of a RadioCard with centered text.

<ExampleTabs name="radio-card-centered" />

### Composition

Here's an example of composing the RadioCard with the `Group` component.

<ExampleTabs name="radio-card-composition" />

### Addon

Pass the `addon` prop to the `RadioCardItem` component to add metadata below the
item content.

<ExampleTabs name="radio-card-with-addon" />

## Props

### Root

<PropTable component="RadioCard" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/radio.mdx

```text
---
title: Radio
description: Used to select one option from several options
links:
  storybook: components-radio--basic
  recipe: radio-group
  ark: https://ark-ui.com/react/docs/components/radio
---

<ExampleTabs name="radio-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`radio` snippet

```sh
npx @chakra-ui/cli snippet add radio
```

The snippet includes a closed component composition for the `RadioGroup`

## Usage

```jsx
import { Radio, RadioGroup } from "@/components/ui/radio"
```

```jsx
<RadioGroup>
  <Radio />
</RadioGroup>
```

## Examples

### Controlled

Use the `value` and `onValueChange` prop to control the selected radio button

<ExampleTabs name="radio-controlled" />

### Colors

Use the `colorPalette` prop to change the color scheme of the component.

<ExampleTabs name="radio-with-colors" />

### Sizes

Use the `size` prop to change the size of the radio component.

<ExampleTabs name="radio-with-sizes" />

### Variants

Use the `variant` prop to change the appearance of the radio component.

<ExampleTabs name="radio-with-variants" />

### Disabled

Use the `disabled` prop to make the radio disabled.

<ExampleTabs name="radio-disabled" />

### Hook Form

Use the `Controller` component from `react-hook-form` to control the radio group
withing a form

<ExampleTabs name="radio-with-hook-form" />

## Props

### Root

<PropTable component="RadioGroup" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/rating.mdx

```text
---
title: Rating
description: Used to show reviews and ratings in a visual format.
links:
  storybook: components-rating--basic
  recipe: rating
  ark: https://ark-ui.com/react/docs/components/rating-group
---

<ExampleTabs name="rating-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`rating` snippet

```sh
npx @chakra-ui/cli snippet add rating
```

The snippet includes a closed component composition for the `RatingGroup`
component.

## Usage

```jsx
import { Rating } from "@/components/ui/rating"
```

```jsx
<Rating />
```

## Examples

### Sizes

Use the `size` prop to change the size of the rating component.

<ExampleTabs name="rating-with-sizes" />

### Controlled

Use the `value` and `onValueChange` prop to control the rating value.

<ExampleTabs name="rating-controlled" />

### ReadOnly

Use the `readOnly` prop to make the rating component read-only.

<ExampleTabs name="rating-with-readonly" />

### Hook Form

Here's an example of how to use rating with `react-hook-form`.

<ExampleTabs name="rating-with-hook-form" />

### Custom Icon

Use the `icon` prop to pass a custom icon to the rating component. This will
override the default star icon.

<ExampleTabs name="rating-with-custom-icon" />

### Half Star

Use the `allowHalf` prop to allow half-star ratings.

<ExampleTabs name="rating-with-half" />

### Emoji

Compose the rating component with emojis.

<ExampleTabs name="rating-emoji" />

### Testimonial

Use the rating component to show testimonials.

<ExampleTabs name="rating-in-testimonial" />

## Props

### Root

<PropTable component="RatingGroup" part="Root" />

### Item

<PropTable component="RatingGroup" part="Item" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/segmented-control.mdx

```text
---
title: Segmented Control
description: Used to pick one choice from a linear set of options
links:
  storybook: components-segmented-control--basic
  recipe: segmented-group
  ark: https://ark-ui.com/react/docs/components/segment-group
---

<ExampleTabs name="segmented-control-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`segmented-control` snippet

```sh
npx @chakra-ui/cli snippet add segmented-control
```

The snippet includes a closed component composition for the `SegmentGroup`
component.

## Usage

```jsx
import { SegmentedControl } from "@/components/ui/segmented-control"
```

```jsx
<SegmentedControl items={[]} />
```

## Examples

### Sizes

Use the `size` prop to change the size of the segmented control.

<ExampleTabs name="segmented-control-with-sizes" />

### Controlled

Use the `value` and `onValueChange` props to control the selected item.

<ExampleTabs name="segmented-control-controlled" />

### Hook Form

Here's an example of how to use the `SegmentedControl` with `react-hook-form`.

<ExampleTabs name="segmented-control-with-hook-form" />

### Disabled

Use the `disabled` prop to disable the segmented control.

<ExampleTabs name="segmented-control-with-disabled" />

### Disabled Item

Use the `disabled` prop on the item to disable it.

<ExampleTabs name="segmented-control-with-disabled-item" />

### Icon

Render the `label` as a `ReactNode` to render an icon.

<ExampleTabs name="segmented-control-with-icon" />

### Card

Here's an example of how to use the `SegmentedControl` within a `Card`.

<ExampleTabs name="segmented-control-in-card" />

## Props

### Root

<PropTable component="SegmentGroup" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/select.mdx

```text
---
title: Select
description: Used to pick a value from predefined options.
links:
  source: components/select
  storybook: components-select--basic
  recipe: select
  ark: https://ark-ui.com/react/docs/components/select
---

<ExampleTabs name="select-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`select` snippet

```sh
npx @chakra-ui/cli snippet add select
```

The snippet includes a closed component composition for the `Select` component.

## Usage

```jsx
import {
  SelectContent,
  SelectItem,
  SelectLabel,
  SelectRoot,
  SelectTrigger,
  SelectValueText,
} from "@/components/ui/select"
```

```jsx
<SelectRoot>
  <SelectLabel />
  <SelectTrigger>
    <SelectValueText />
  </SelectTrigger>
  <SelectContent>
    <SelectItem />
  </SelectContent>
</SelectRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the select component.

<ExampleTabs name="select-with-sizes" />

### Variants

Use the `variant` prop to change the appearance of the select component.

<ExampleTabs name="select-with-variants" />

### Option Group

Use the `SelectItemGroup` component to group select options.

<ExampleTabs name="select-with-option-group" />

### Controlled

Use the `value` and `onValueChange` props to control the select component.

<ExampleTabs name="select-controlled" />

### Async Loading

Here's an example of how to populate the select `collection` from a remote
source.

<ExampleTabs name="select-async-loading" />

### Hook Form

Here's an example of how to use the `Select` component with `react-hook-form`.

<ExampleTabs name="select-with-hook-form" />

### Disabled

Use the `disabled` prop to disable the select component.

<ExampleTabs name="select-with-disabled" />

### Invalid

Here's an example of how to compose the `Select` component with the `Field`
component to display an error state.

<ExampleTabs name="select-with-invalid" />

### Multiple

Use the `multiple` prop to allow multiple selections.

<ExampleTabs name="select-with-multiple" />

### Positioning

Use the `positioning` prop to control the underlying `floating-ui` options of
the select component.

<ExampleTabs name="select-with-positioning" />

### Within Popover

Here's an example of how to use the `Select` within a `Popover` component.

<ExampleTabs name="select-in-popover" />

### Within Dialog

Here's an example of how to use the `Select` within a `Dialog` component.

> Due to the focus trap within the dialog, it's important to change the portal
> target from the document's body to the dialog's content.

<ExampleTabs name="select-in-dialog" />

### Avatar Select

Here's an example of how to compose the `Select` and the `Avatar`.

<ExampleTabs name="select-with-avatar" />

### Clear Trigger

Pass the `clearable` prop to the `SelectTrigger`.

<ExampleTabs name="select-with-clear" />

### Overflow

<ExampleTabs name="select-with-overflow" />

## Props

### Root

<PropTable component="Select" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/separator.mdx

```text
---
title: Separator
description: Used to visually separate content
links:
  source: components/separator
  storybook: components-separator--basic
  recipe: separator
---

<ExampleTabs name="separator-basic" />

## Usage

```jsx
import { Separator } from "@chakra-ui/react"
```

```jsx
<Separator />
```

## Examples

### Variants

Use the `variant` prop to change the appearance of the separator.

<ExampleTabs name="separator-with-variants" />

### Sizes

Use the `size` prop to change the size of the separator.

<ExampleTabs name="separator-with-sizes" />

### Label

Use the `label` prop to add a label to the separator.

<ExampleTabs name="separator-with-label" />

### Vertical

Use the `orientation` prop to change the orientation of the separator.

<ExampleTabs name="separator-vertical" />

## Props

<PropTable component="Separator" part="Separator" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/show.mdx

```text
---
title: Show
description: Used to conditional render part of the view based on a condition.
---

<ExampleTabs name="show-basic" />

## Usage

The `Show` component renders its children when the `when` value is truthy,
otherwise it renders the `fallback` prop.

```jsx
import { Show } from "@chakra-ui/react"
```

```jsx
<Show when={...} fallback={...}>
  <div>Content</div>
</Show>
```

## Examples

### Fallback

Use the `fallback` prop to render a fallback component when the array is empty
or undefined.

<ExampleTabs name="show-with-fallback" />

### Render Prop

Use the `children` render prop to narrow the type of the `when` value and remove
`undefined` | `null`

<ExampleTabs name="show-with-render-prop" />

## Props

<PropTable component="Show" part="Show" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/simple-grid.mdx

```text
---
title: SimpleGrid
description:
  SimpleGrid provides a friendly interface to create responsive grid layouts
  with ease.
---

<ExampleTabs name="simple-grid-basic" />

## Usage

The `SimpleGrid` component allows you to create responsive grid layouts with
ease.

```jsx
import { SimpleGrid } from "@chakra-ui/react"
```

```jsx
<SimpleGrid>
  <Box />
  <Box />
</SimpleGrid>
```

## Examples

### Columns

Specify the number of columns for the grid layout using the `columns` prop.

<ExampleTabs name="simple-grid-with-columns" />

### Auto-responsive

Make the grid responsive and adjust automatically without passing columns, by
using the `minChildWidth` prop. This uses css grid auto-fit and minmax()
internally.

<ExampleTabs name="simple-grid-with-autofit" />

### Column Span

Specify the size of the column by using the `colSpan` prop.

<ExampleTabs name="simple-grid-with-col-span" />

### Row and Column Gap

Pass the `rowGap` and `columnGap` props to change the row and column spacing
between the grid items.

<ExampleTabs name="simple-grid-with-row-and-col-gap" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/skeleton.mdx

```text
---
title: Skeleton
description: Used to render a placeholder while the content is loading.
links:
  source: components/skeleton
  storybook: components-skeleton--basic
  recipe: skeleton
---

<ExampleTabs name="skeleton-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`skeleton` snippet

```sh
npx @chakra-ui/cli snippet add skeleton
```

The snippet includes components for skeleton text and circle using the
`Skeleton` component.

## Usage

```jsx
import {
  Skeleton,
  SkeletonCircle,
  SkeletonText,
} from "@/components/ui/skeleton"
```

```jsx
<Stack gap="6" maxW="xs">
  <HStack width="full">
    <SkeletonCircle size="10" />
    <SkeletonText noOfLines={2} />
  </HStack>
  <Skeleton height="200px" />
</Stack>
```

## Examples

### Feed

Use the `Skeleton` component to create a feed skeleton.

<ExampleTabs name="skeleton-for-feed" />

### Text

Use the `SkeletonText` component to create a skeleton for text.

<ExampleTabs name="skeleton-for-text" />

### With Children

Use the `loading` prop to show the skeleton while the content is loading.

<ExampleTabs name="skeleton-with-children" />

### Variants

Use the `variant` prop to change the visual style of the Skeleton.

<ExampleTabs name="skeleton-with-variants" />

### Content Loading

When `loading` is changed to `false`, the Skeleton component will fade in.

<ExampleTabs name="skeleton-with-loaded" />

## Props

<PropTable component="Skeleton" part="Skeleton" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/slider.mdx

```text
---
title: Slider
description: Used to allow users to make selections from a range of values.
links:
  source: components/slider
  storybook: components-slider--basic
  recipe: slider
  ark: https://ark-ui.com/react/docs/components/slider
---

<ExampleTabs name="slider-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`slider` snippet

```sh
npx @chakra-ui/cli snippet add slider
```

The snippet includes a closed component composition for the `Slider` component.

## Usage

```jsx
import { Slider } from "@/components/ui/slider"
```

```jsx
<Slider defaultValue={[40]} />
```

## Examples

### Sizes

Use the `size` prop to change the size of the slider.

<ExampleTabs name="slider-with-sizes" />

### Variants

Use the `variant` prop to change the visual style of the slider.

<ExampleTabs name="slider-with-variants" />

### Colors

Use the `colorPalette` prop to change the color of the slider.

<ExampleTabs name="slider-with-colors" />

### Label

Use the `label` prop to add a label to the slider.

<ExampleTabs name="slider-with-label" />

### Range Slider

Set the `value` or `defaultValue` prop to an array to create a range slider.

<ExampleTabs name="slider-with-multiple-thumbs" />

### Controlled

Use the `value` and `onValueChange` props to control the value of the slider.

<ExampleTabs name="slider-controlled" />

### Hook Form

Here's an example of how to integrate a slider with `react-hook-form`.

<ExampleTabs name="slider-with-hook-form" />

### Disabled

Use the `disabled` prop to disable the slider.

<ExampleTabs name="slider-disabled" />

### Change End

Use the `onValueChangeEnd` prop to listen to the end of the slider change.

<ExampleTabs name="slider-change-end" />

### Steps

Use the `step` prop to set the step value of the slider.

<ExampleTabs name="slider-with-step" />

### Thumb Contained

Use the `thumbAlignment` and `thumbSize` prop to contain the thumb within the
track.

<ExampleTabs name="slider-thumb-contained" />

### Marks

Use the `marks` prop to display marks on the slider.

<ExampleTabs name="slider-with-marks" />

You can also add labels to the marks using the `marks` prop.

<ExampleTabs name="slider-with-marks-and-label" />

### Vertical

Use the `orientation` prop to change the orientation of the slider.

<ExampleTabs name="slider-vertical" />

### Vertical with Marks

Here's an example of a vertical slider with marks.

<ExampleTabs name="slider-with-marks-vertical" />

## Props

<PropTable component="Slider" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/spinner.mdx

```text
---
title: Spinner
description: Used to provide a visual cue that an action is processing
links:
  source: components/spinner
  storybook: components-spinner--basic
  recipe: spinner
---

<ExampleTabs name="spinner-basic" />

## Usage

```jsx
import { Spinner } from "@chakra-ui/react"
```

```jsx
<Spinner />
```

## Examples

### Sizes

Use the `size` prop to change the size of the spinner.

<ExampleTabs name="spinner-with-sizes" />

### Colors

Use the `colorPalette` prop to change the color scheme of the spinner.

<ExampleTabs name="spinner-with-colors" />

### Custom Color

Use the `color` prop to pass a custom color to the spinner.

<ExampleTabs name="spinner-custom-color" />

### Track Color

Use the `--spinner-track-color` variable to change the color of the spinner's
track.

<ExampleTabs name="spinner-with-track-color" />

### Custom Speed

Use the `animationDuration` prop to change the speed of the spinner.

<ExampleTabs name="spinner-with-custom-speed" />

### Thickness

Use the `borderWidth` prop to change the thickness of the spinner.

<ExampleTabs name="spinner-with-custom-thickness" />

### Label

Compose the spinner with a label to provide additional context.

<ExampleTabs name="spinner-with-label" />

### Overlay

Compose spinner with the `AbsoluteCenter` component to overlay the spinner on
top of another component.

<ExampleTabs name="spinner-with-overlay" />

## Props

### Root

<PropTable component="Spinner" part="Spinner" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/stack.mdx

```text
---
title: Stack
description: Used to layout its children in a vertical or horizontal stack.
links:
  source: components/stack
---

<ExampleTabs name="stack-basic" />

## Usage

By default, Stack applies `flex-direction: column` and `gap: 8px` to its
children.

```jsx
import { HStack, Stack, VStack } from "@chakra-ui/react"
```

```jsx
<Stack>
  <div />
  <div />
</Stack>
```

## Examples

### Horizontal

Use the `direction` prop to change the direction of the stack.

<ExampleTabs name="stack-horizontal" />

### HStack

Alternatively, you can use the `HStack` to create a horizontal stack and align
its children horizontally.

<ExampleTabs name="stack-with-hstack" />

### VStack

Use the `VStack` to create a vertical stack and align its children vertically.

<ExampleTabs name="stack-with-vstack" />

### Separator

Use the `separator` prop to add a separator between the stack items.

<ExampleTabs name="stack-with-separator" />

### Responsive Direction

Use the `direction` prop to change the direction of the stack responsively.

<ExampleTabs name="stack-with-responsive-direction" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/stat.mdx

```text
---
title: Stat
description: Used to display a statistic with a title and value.
links:
  source: components/stat
  storybook: components-stat--basic
  recipe: stat
---

<ExampleTabs name="stat-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`stat` snippet

```sh
npx @chakra-ui/cli snippet add stat
```

The snippet includes a closed component composition for the `Stat` component.

## Usage

```jsx
import { StatLabel, StatRoot, StatValueText } from "@/components/ui/stat"
```

```jsx
<StatRoot>
  <StatLabel />
  <StatValueText />
  <StatHelpText>
    <StatUpIndicator />
    <StatDownIndicator />
  </StatHelpText>
</StatRoot>
```

## Examples

### Format Options

Pass the `formatOptions` to the `StatValueText` component to format the value.

<ExampleTabs name="stat-with-format-options" />

### Indicator

Here's an example of how to display a statistic with an indicator.

<ExampleTabs name="stat-with-indicator" />

### Info Tip

Pass the `info` prop to the `StatLabel` component to display an info tip.

<ExampleTabs name="stat-with-info-tip" />

### Value Unit

Here's an example of how to display a value with a unit.

<ExampleTabs name="stat-with-value-unit" />

### Progress Bar

Here's an example of how to display a statistic with a progress bar.

<ExampleTabs name="stat-with-progress-bar" />

### Icon

Here's an example of how to display a statistic with an icon.

<ExampleTabs name="stat-with-icon" />

### Trend

Here's an example of how to display a statistic with a trend indicator.

<ExampleTabs name="stat-with-trend" />

## Props

### Root

<PropTable component="Stat" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/status.mdx

```text
---
title: Status
description: Used to indicate the status of a process or state
links:
  source: components/status
  storybook: components-status--basic
  recipe: status
---

<ExampleTabs name="status-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`status` snippet

```sh
npx @chakra-ui/cli snippet add status
```

The snippet includes a closed component composition for the `Status` component.

## Usage

```jsx
import { Status } from "@/components/ui/status"
```

```jsx
<Status>Label</Status>
```

## Examples

### Label

Render the label within the status component.

<ExampleTabs name="status-with-label" />

### Sizes

Use the `size` prop to change the size of the status component.

<ExampleTabs name="status-with-sizes" />

## Props

<PropTable component="Status" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/steps.mdx

```text
---
title: Steps
description: Used to indicate progress through a multi-step process
links:
  source: components/steps
  storybook: components-steps--basic
  recipe: steps
  ark: https://ark-ui.com/react/docs/components/steps
---

<ExampleTabs name="steps-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`steps` snippet

```sh
npx @chakra-ui/cli snippet add steps
```

The snippet includes a closed component composition for the `Steps` component.

## Usage

```jsx
import {
  StepsCompletedContent,
  StepsContent,
  StepsItem,
  StepsList,
  StepsNextTrigger,
  StepsPrevTrigger,
  StepsRoot,
} from "@/components/ui/steps"
```

```jsx
<StepsRoot>
  <StepsList>
    <StepsItem />
  </StepsList>
  <StepsContent />
  <StepsCompleteContent />
  <StepsPrevTrigger />
  <StepsNextTrigger />
</StepsRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the steps component.

<ExampleTabs name="steps-with-sizes" />

### Variants

Use the `variant` prop to change the appearance of the steps component.

<ExampleTabs name="steps-with-variants" />

### Colors

Use the `colorPalette` prop to change the color scheme of the component.

<ExampleTabs name="steps-with-colors" />

### Vertical

Use the `orientation` prop to change the orientation of the steps component.

<ExampleTabs name="steps-vertical" />

### Icon

Pass the `icon` prop to the `StepsItem` component to display an icon.

<ExampleTabs name="steps-with-icon" />

### Description

Pass the `description` prop to the `StepsItem` component to display a
description.

<ExampleTabs name="steps-with-description" />

## Props

### Root

<PropTable component="Steps" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/switch.mdx

```text
---
title: Switch
description: Used to capture a binary state
links:
  source: components/switch
  storybook: components-switch--basic
  recipe: switch
  ark: https://ark-ui.com/react/docs/components/switch
---

<ExampleTabs name="switch-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`switch` snippet

```sh
npx @chakra-ui/cli snippet add switch
```

The snippet includes a closed component composition for the `Switch` component.

## Usage

```jsx
import { Switch } from "@/components/ui/switch"
```

```jsx
<Switch>Label</Switch>
```

## Examples

### Sizes

Use the `size` prop to change the size of the switch component.

<ExampleTabs name="switch-with-sizes" />

### Variants

Use the `variant` prop to change the visual style of the switch.

<ExampleTabs name="switch-with-variants" />

### Colors

Use the `colorPalette` prop to change the color scheme of the component.

<ExampleTabs name="switch-with-colors" />

### Controlled

Use the `checked` and `onCheckedChange` prop to control the state of the switch.

<ExampleTabs name="switch-controlled" />

### Hook Form

Here's an example of integrating the switch with `react-hook-form`.

<ExampleTabs name="switch-with-hook-form" />

### Disabled

Use the `disabled` prop to disable the switch.

<ExampleTabs name="switch-with-disabled" />

### Invalid

Use the `invalid` prop to indicate an error state for the switch.

<ExampleTabs name="switch-with-invalid" />

### Tooltip

Here's an example of a switch with a tooltip.

<ExampleTabs name="switch-with-tooltip" />

### Track Label

Use the `trackLabel` prop to display different label based on the checked state.

<ExampleTabs name="switch-with-track-label" />

### Thumb Label

Use the `thumbLabel` prop to add an icon to the switch thumb.

<ExampleTabs name="switch-with-thumb-label" />

## Props

### Root

<PropTable component="Switch" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/table.mdx

```text
---
title: Table
description: Used to display data in a tabular format.
links:
  source: components/table
  storybook: components-table--basic
  recipe: table
---

<ExampleTabs name="table-basic" />

## Usage

```jsx
import { Table } from "@chakra-ui/react"
```

```jsx
<Table.Root>
  <Table.Header>
    <Table.Row>
      <Table.ColumnHeader />
    </Table.Row>
  </Table.Header>
  <Table.Body>
    <Table.Row>
      <Table.Cell />
    </Table.Row>
  </Table.Body>
  <Table.Footer>
    <Table.Row>
      <Table.Cell />
    </Table.Row>
  </Table.Footer>
</Table.Root>
```

## Examples

### Sizes

Use the `size` prop to change the size of the table.

<ExampleTabs name="table-with-sizes" />

### Variants

Use the `variant` prop to change the appearance of the table.

<ExampleTabs name="table-with-variants" />

### Striped

Use the `striped` prop to add zebra-stripes to the table.

<ExampleTabs name="table-with-striped" />

### Column Border

Use the `showColumnBorder` prop to add borders between columns.

<ExampleTabs name="table-with-column-border" />

### Overflow

Render the `Table.ScrollArea` component to enable horizontal scrolling.

<ExampleTabs name="table-with-overflow" />

### Sticky Header

Use the `stickyHeader` prop to make the table header sticky.

<ExampleTabs name="table-with-sticky-header" />

### Highlight on Hover

Use the `interactive` prop to highlight rows on hover.

<ExampleTabs name="table-with-interactive" />

### Pagination

Here's an example of how to compose a table with pagination.

<ExampleTabs name="table-with-pagination" />

### Action Bar

Here's an example of how to compose a table with an action bar and checkboxes.
This is useful for showing actions for selected table rows.

<ExampleTabs name="table-with-selection-action-bar" />

### Column Group

Use the `Table.ColumnGroup` component to distribute the column widths using the
html `colgroup` element.

:::warning

The only prop that works for this component is `htmlWidth`

:::

<ExampleTabs name="table-with-column-group" />

## Props

### Root

<PropTable component="Table" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/tabs.mdx

```text
---
title: Tabs
description: Used to display content in a tabbed interface
links:
  source: components/tabs
  storybook: components-tabs--basic
  recipe: tabs
  ark: https://ark-ui.com/react/docs/components/tabs
---

<ExampleTabs name="tabs-basic" />

## Usage

```jsx
import { Tabs } from "@chakra-ui/react"
```

```jsx
<Tabs.Root>
  <Tabs.List>
    <Tabs.Trigger />
    <Tabs.Indicator />
  </Tabs.List>
  <Tabs.Content />
</Tabs.Root>
```

## Examples

### Variants

Use the `variant` prop to change the visual style of the tabs.

<ExampleTabs name="tabs-with-variants" />

### Lazy Mounted

Use the `lazyMount` and/or `unmountOnExit` prop to only render the tab content
when it is active. This can be useful for performance optimization.

<ExampleTabs name="tabs-lazy-mounted" />

### Indicator

Render the `Tabs.Indicator` component to display a visual indicator of the
active tab.

<ExampleTabs name="tabs-with-indicator" />

### Links

Pass the `asChild` to the `Tabs.Trigger` component to render a link as a tab.
When a tab is clicked, the link will be navigated to.

<ExampleTabs name="tabs-with-links" />

### Fitted

Use the `fitted` prop to make the tabs fit the width of the container.

<ExampleTabs name="tabs-with-fitted" />

### Controlled

Use the `value` and `onValueChange` prop to control the active tab.

<ExampleTabs name="tabs-controlled" />

### Disabled Tab

Set the `disabled` prop on the `Tabs.Trigger` component to disable a tab.

<ExampleTabs name="tabs-with-disabled-tab" />

### Manual activation

By default, the tabs are selected when the arrow keys are pressed. Disable this
behavior by setting the `activationBehavior` prop to `manual`.

In this mode, the tabs will only be selected when clicked or the enter key is
pressed.

<ExampleTabs name="tabs-with-manual-activation" />

### Dynamic

Here's an example of how to dynamically add and remove tabs.

<ExampleTabs name="tabs-with-dynamic-add" />

## Props

### Root

<PropTable component="Tabs" part="Root" />

### Trigger

<PropTable component="Tabs" part="TabTrigger" />

### Content

<PropTable component="Tabs" part="TabContent" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/tag.mdx

```text
---
title: Tag
description: Used for categorizing or labeling content
links:
  source: components/tag
  storybook: components-tag--basic
  recipe: tag
---

<ExampleTabs name="tag-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`tag` snippet

```sh
npx @chakra-ui/cli snippet add tag
```

The snippet includes a closed component composition for the `Tag` component.

## Usage

```jsx
import { Tag } from "@/components/ui/tag"
```

```jsx
<Tag>Tag here</Tag>
```

## Examples

### Icon

Use the `startElement` prop to add an icon to the tag.

<ExampleTabs name="tag-with-icon" />

### Variants

Use the `variant` prop to change the appearance of the tag.

<ExampleTabs name="tag-with-variants" />

### Sizes

Use the `size` prop to change the size of the tag.

<ExampleTabs name="tag-with-sizes" />

### Colors

Use the `colorPalette` prop to change the color of the tag.

<ExampleTabs name="tag-with-colors" />

### Closable

Use the `closable` prop to make the tag closable.

<ExampleTabs name="tag-with-close" />

### Overflow

Use the `maxWidth` prop to control the maximum width of the tag. When the
content exceeds this width, it will be truncated with an ellipsis.

> This is particularly useful when dealing with dynamic or user-generated
> content where the length might vary.

<ExampleTabs name="tag-with-overflow" />

### Avatar

The tag component has been designed to work well with the `Avatar` component.

> Note: Set the avatar size to `full` to ensure it's sized correctly.

<ExampleTabs name="tag-with-avatar" />

### Render as button

Use the `asChild` prop to render the tag as a button.

> Note: Import the Tag from `@chakra-ui/react` to use this prop.

<ExampleTabs name="tag-as-button" />

## Props

### Root

<PropTable component="Tag" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/text.mdx

```text
---
title: Text
description: Used to render text and paragraphs within an interface.
links:
  source: components/typography
---

<ExampleTabs name="text-basic" />

## Usage

```jsx
import { Text } from "@chakra-ui/react"
```

```jsx
<Text>This is the text component</Text>
```

## Examples

### Sizes

Use the `fontSize` or `textStyle` prop to change the size of the text.

<ExampleTabs name="text-with-sizes" />

### Weights

Use the `fontWeight` prop to change the weight of the text.

<ExampleTabs name="text-with-weights" />

### Truncation

Use the `truncate` prop to truncate the text after a single line.

<ExampleTabs name="text-with-truncate" />

### Line Clamp

Use the `lineClamp` prop to truncate the text after a certain number of lines.

<ExampleTabs name="text-with-line-clamp" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/textarea.mdx

```text
---
title: Textarea
description: Used to enter multiple lines of text.
links:
  source: components/textarea
  storybook: components-textarea--basic
  recipe: textarea
---

<ExampleTabs name="textarea-basic" />

## Usage

```jsx
import { Textarea } from "@chakra-ui/react"
```

```jsx
<Textarea placeholder="..." />
```

## Examples

### Variants

Use the `variant` prop to change the appearance of the textarea.

<ExampleTabs name="textarea-with-variants" />

### Sizes

Use the `size` prop to change the size of the textarea.

<ExampleTabs name="textarea-with-sizes" />

### Helper Text

Pair the textarea with the `Field` component to add helper text.

<ExampleTabs name="textarea-with-helper-text" />

### Error Text

Pair the textarea with the `Field` component to add error text.

<ExampleTabs name="textarea-with-error-text" />

### Field

Compose the textarea with the `Field` component to add a label, helper text, and
error text.

<ExampleTabs name="input-with-field" />

### Hook Form

Here's an example of how to integrate the textarea with `react-hook-form`.

<ExampleTabs name="textarea-with-hook-form" />

### Resize

Use the `resize` prop to control the resize behavior of the textarea.

<ExampleTabs name="textarea-with-resize" />

### Autoresize

Use the `autoresize` prop to make the textarea autoresize vertically as you
type.

<ExampleTabs name="textarea-with-autoresize" />

## Props

<PropTable component="Textarea" part="Textarea" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/theme.mdx

```text
---
title: Theme
description: Used to force a part of the tree to light or dark mode.
---

<ExampleTabs name="theme-basic" />

## Usage

```jsx
import { Theme } from "@chakra-ui/react"
```

```jsx
<Theme appearance="dark">
  <div />
</Theme>
```

## Examples

### Nested

The theme can be nested to apply different appearances to different parts of the
tree. This is useful for applying a global appearance and then overriding some
parts of it.

> Good to know: We use native CSS selectors to achieve this.

<ExampleTabs name="theme-nested" />

### Portalled

Use the `asChild` prop to force the appearance of portalled elements like the
popover and modal content.

<ExampleTabs name="theme-with-portalled" />

### Page Specific Color Mode

To lock a page to a specific color mode (light or dark), wrap the entire page
with the `Theme` component.

You can also combine it with the `ColorModeProvider` if you use the
`useColorMode` hook.

```tsx
import { ColorModeProvider } from "@/components/ui/color-mode"
import { Theme } from "@chakra-ui/react"

export const ForcedColorMode = ({ children }) => {
  return (
    <ColorModeProvider forcedTheme="dark">
      <Theme appearance="dark">{/* Rest of the page */}</Theme>
    </ColorModeProvider>
  )
}
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/timeline.mdx

```text
---
title: Timeline
description: Used to display a list of events in chronological order
links:
  source: components/timeline
  storybook: components-timeline--basic
  recipe: timeline
---

<ExampleTabs name="timeline-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`timeline` snippet

```sh
npx @chakra-ui/cli snippet add timeline
```

The snippet includes a closed component composition for the `Timeline`
component.

## Usage

```jsx
import { TimelineItem, TimelineRoot } from "@/components/ui/timeline"
```

```jsx
<TimelineRoot>
  <TimelineItem />
  <TimelineItem />
</TimelineRoot>
```

## Examples

### Sizes

Use the `size` prop to change the size of the timeline.

<ExampleTabs name="timeline-with-sizes" />

### Variants

Use the `variant` prop to change the variant of the timeline.

<ExampleTabs name="timeline-with-variants" />

### Content Before

Here's an example of a timeline with content before the timeline indicator.

<ExampleTabs name="timeline-with-content-before" />

### Alternating Content

Here's an example of a timeline with alternating content.

<ExampleTabs name="timeline-alternating" />

### Composition

Here's an example of how to compose the timeline with other components to create
a consistent-looking timeline.

<ExampleTabs name="timeline-composition" />

## Props

### Root

<PropTable component="Timeline" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/toast.mdx

```text
---
title: Toast
description: Used to display a temporary message to the user
links:
  source: components/toast
  storybook: components-toast--basic
  recipe: toast
  ark: https://ark-ui.com/react/docs/components/toast
---

<ExampleTabs name="toaster-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`toaster` snippet

```sh
npx @chakra-ui/cli snippet add toaster
```

The snippet includes a closed component composition for the `Toast` component.

## Usage

```jsx
import { Toaster, toaster } from "@/components/ui/toaster"
```

First, render the `Toaster` component in your app.

```jsx
<Toaster />
```

Then, create a toast by calling the `toaster` function.

```jsx
toaster.create({
  title: "Toast Title",
  description: "Toast Description",
})
```

## Examples

### Persistent Toast

Set the `type` prop to `"loading"` to create a persistent toast.

<ExampleTabs name="toaster-persistent" />

### Types

Here's an example of each type of toast.

<ExampleTabs name="toaster-with-status" />

### With Action

Use the `action` and `actionLabel` prop to add an action to the toast.

> When the action trigger is clicked, the toast will be closed.

<ExampleTabs name="toaster-with-action" />

### Promise

Use the `toaster.promise` to create a toast that resolves when the promise is
resolved.

Next, you can define the toast options (title, description, etc.) for each state
of the promise.

<ExampleTabs name="toaster-with-promise" />

### Custom Duration

Use the `duration` prop to set the duration of the toast.

<ExampleTabs name="toaster-with-duration" />

### Pause and Play

Use the `pause` and `resume` methods on the `toaster` object to pause and play
the toast.

Pausing a toast prevents it from timing out, while resuming it will reenable the
timeout using the remaining duration.

<ExampleTabs name="toaster-pause-and-play" />

### Lifecycle

Use the `onStatusChange` prop on the `toaster` function to listen for changes to
the toast's status.

<ExampleTabs name="toaster-lifecycle" />

### Maximum Visible Toasts

Set the `max` prop on the `createToaster` function to define the maximum number
of toasts that can be rendered at any one time. Any extra toasts will be queued
and rendered when a toast has been dismissed.

```jsx title="@/components/ui/toaster.tsx"
const toaster = createToaster({
  max: 3,
})
```

### Placement

Toasts can be displayed on all four corners of a page. We recommend picking one
desired position and configure it in the `createToaster` function.

```jsx title="@/components/ui/toaster.tsx"
const toaster = createToaster({
  placement: "top-end",
})
```

### Overlapping Toasts

By default, toasts are stacked on top of each other. To make the toasts overlap
each other, set the `overlap` prop to `true` in the `createToaster` function.

```jsx title="@/components/ui/toaster.tsx"
const toaster = createToaster({
  placement: "top-end",
  overlap: true,
})
```

### Offset

Set the `offset` prop in the `createToaster` function to offset the toasts from
the edges of the screen.

```jsx title="@/components/ui/toaster.tsx"
const toaster = createToaster({
  offset: "20px",
})
```

Alternatively, you can use the `offset` prop to set the offset for each edge of
the screen.

```jsx title="@/components/ui/toaster.tsx"
const toaster = createToaster({
  offset: { left: "20px", top: "20px", right: "20px", bottom: "20px" },
})
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/toggle-tip.mdx

```text
---
title: Toggle Tip
description: Looks like a tooltip, but works like a popover.
---

<ExampleTabs name="toggle-tip-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`toggle-tip` snippet

```sh
npx @chakra-ui/cli snippet add toggle-tip
```

The snippet includes a closed component composition for the `Popover` component.

## Usage

```jsx
import { InfoTip, ToggleTip } from "@/components/ui/toggle-tip"
```

```jsx
<ToggleTip content="...">
  <button />
</ToggleTip>
```

## Examples

### Info Tip

Use the `InfoTip` component to display an info tip. This component renders an
icon button with an info icon by default.

> Useful for landing pages to display additional information about a feature.

<ExampleTabs name="toggle-tip-info-tip" />

## Props

### Root

<PropTable component="Popover" part="Root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/tooltip.mdx

```text
---
title: Tooltip
description:
  Used to display additional information when a user hovers over an element.
links:
  source: components/tooltip
  storybook: components-tooltip--basic
  recipe: tooltip
  ark: https://ark-ui.com/react/docs/components/tooltip
---

<ExampleTabs name="tooltip-basic" />

## Setup

If you don't already have the snippet, run the following command to add the
`tooltip` snippet

```sh
npx @chakra-ui/cli snippet add tooltip
```

The snippet includes a closed component composition for the `Tooltip` component.

## Usage

```jsx
import { Tooltip } from "@/components/ui/tooltip"
```

```jsx
<Tooltip content="...">
  <button />
</Tooltip>
```

## Examples

### Arrow

Use the `showArrow` prop to show an arrow on the tooltip.

<ExampleTabs name="tooltip-with-arrow" />

### Placement

Use the `positioning.placement` prop to change the position of the tooltip.

<ExampleTabs name="tooltip-with-placement" />

### Offset

Use the `positioning.offset` prop to change the offset of the tooltip.

<ExampleTabs name="tooltip-with-offset" />

### Delay

Use the `openDelay` and `closeDelay` prop to change the delay of the tooltip.

<ExampleTabs name="tooltip-with-delay" />

### Custom Background

Use the `--tooltip-bg` CSS variable to change the background color of the
tooltip.

<ExampleTabs name="tooltip-with-custom-bg" />

### Controlled

Use the `open` and `onOpenChange` prop to control the visibility of the tooltip.

<ExampleTabs name="tooltip-controlled" />

### Interactive

Use the `interactive` prop to keep the tooltip open when interacting with its
content.

<ExampleTabs name="tooltip-with-interactive" />

### Disabled

Use the `disabled` prop to disable the tooltip. When disabled, the tooltip will
not be shown.

<ExampleTabs name="tooltip-with-disabled" />

### With Avatar

Here's an example of how to use the `Tooltip` component with an `Avatar`
component.

<ExampleTabs name="tooltip-with-avatar" />

### With Checkbox

Here's an example of how to use the `Tooltip` component with a `Checkbox`
component.

<ExampleTabs name="tooltip-with-checkbox" />

### With MenuItem

Here's an example of how to use the `Tooltip` with a `MenuItem` component.

<ExampleTabs name="tooltip-with-menu-item" />

### With Switch

Here's an example of how to wrap `Tooltip` around a `Switch` component.

<ExampleTabs name="tooltip-with-switch" />

### With Tabs

Here's an example of how to wrap `Tooltip` around a `Tabs` component.

<ExampleTabs name="tooltip-with-tab" />

### Without Snippet

If you don't want to use the snippet, you can use the `Tooltip` component from
the `@chakra-ui/react` package.

<ExampleTabs name="tooltip-without-snippet" />

## Props

### Root

<PropTable component="tooltip" part="root" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/components/visually-hidden.mdx

```text
---
title: Visually Hidden
description:
  Used to hide content visually but keep it accessible to screen readers.
---

<ExampleTabs name="visually-hidden-basic" />

## Usage

```jsx
import { VisuallyHidden } from "@chakra-ui/react"
```

```jsx
<VisuallyHidden>Hidden content</VisuallyHidden>
```

## Examples

### Input

Using the `asChild` prop, you can pass a child element to the `VisuallyHidden`
component.

<ExampleTabs name="visually-hidden-with-input" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/get-started/cli.mdx

```text
---
title: "CLI"
description:
  Learn more to use the Chakra CLI command to generate snippets and typings
---

The Chakra UI CLI can be used to generate typings for your custom theme tokens,
like colors, semantic tokens, recipe variants, etc.

## Installation

In order to use the CLI, you need to install it as a dev dependency in your
project:

:::code-group

```bash [npm]
npm i -D @chakra-ui/cli
```

```bash [pnpm]
pnpm add -D @chakra-ui/cli
```

```bash [bun]
bun add -d @chakra-ui/cli
```

:::

:::warning

To use the CLI tool, please ensure that the version of Node.js is `>= 20.6.0`.

:::

## Usage

Use the Chakra CLI to run any of the commands listed below with your preferred
package manager.

```bash
Usage: chakra-ui [options] [command]

The official CLI for Chakra UI projects

Options:
  -V, --version               output the version number
  -h, --help                  display help for command

Commands:
  typegen [options] <source>  Generate theme and recipe typings
  snippet                     Add snippets to your project for better DX
  eject [options]             Take control over the default theme tokens and recipes
  help [command]              display help for command
```

## `chakra typegen`

Generates theme and recipe typings for your custom theme. This helps to provide
autocompletion and type safety in your project.

```bash
# Generate typings
chakra typegen src/theme.ts

# Watch for changes and rebuild
chakra typegen src/theme.ts --watch

# Generate strict types for props variant and size
chakra typegen src/theme.ts --strict
```

## `chakra snippet`

Generates useful component compositions that boost your development speed.

```bash
# Add all snippets
chakra snippet add --all

# Add a specific snippet
chakra snippet add button

# List all available snippets
chakra snippet list

# Specify a custom directory
chakra snippet --outdir ./components/custom
```

## `chakra eject`

Generated the file(s) that contain the default theme tokens and recipes so you
can have full control over them.

```bash
# Copy the tokens and recipes to your project
chakra eject --outdir src/theme
```

## FAQ

### Autocomplete for custom tokens not working?

After generating the typings, you need to "Restart TS Server" for the
autocomplete to show up.

Alternatively, you can install the `@chakra-ui/cli` package locally as a dev
dependency and run the `chakra typegen` command to generate the typings.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/get-started/contributing.mdx

```text
---
title: Contributing to Chakra UI
description: The guide to contributing to Chakra UI v3.0
---

Thanks for showing interest to contribute to Chakra UI 💖, you rock!

## Overview

Here are a few ways you can help improve Chakra UI

- **Improve the documentation**: Add new demos, fix typos, or add missing
  information.
- **Add new demos**: Add new component demos to the website and storybook. Open
  a PR to `apps/compositions/src/examples`
- **Fix bugs**: Report bugs, fix bugs, or add missing features.
- **Contribute to the code**: Propose new features by opening a Github
  Discussion, or find existing bugs to work on.
- **Improve the code**: Improve the code, fix bugs, or add missing features.

:::info

We welcome all contributions, no matter how big or small.

:::

## Architecture

Chakra v3.x is a composition of two projects in the Chakra ecosystem, Ark UI and
Zag.js. The goal is to maintain as little code as possible in Chakra UI, and
delegate the heavy lifting to these projects.

:::card-group

<ResourceCard
  type="github"
  title="Zag.js"
  description="Component logic modelled as a state machine"
  url="https://github.com/chakra-ui/zag"
/>

<ResourceCard
  type="github"
  title="Ark UI"
  description="State machine from Zag.js converted to headless UI components"
  url="https://github.com/chakra-ui/ark"
/>

:::

### Filing Issues

The mindset for filing issues on Chakra v3.x works like this:

- If the issue is a logic or accessibility bug, then it's most likely a bug in
  Zag.js. Consider opening an issue in the Zag.js repository.

- If it's a styling issue, then you can fix it directly in the Chakra UI repo.

### Feature Requests

The mindset for filing feature requests on Chakra v3.x works like this:

- If the feature is a new component without logic, then it can go in Chakra UI
  or Ark UI. Start a discussion on the
  [Chakra UI repository](https://github.com/chakra-ui/chakra-ui)

- If the feature is a new component with logic, it belongs in Zag.js. Start a
  discussion on the [Zag.js repository](https://github.com/chakra-ui/zag).

## Local Setup

- Clone the repository

```bash
git clone https://github.com/chakra-ui/chakra-ui.git
```

- Install dependencies with pnpm

```bash
pnpm install
```

- Build local version of all packages

```bash
pnpm build:fast
```

- Start storybook

```bash
pnpm storybook
```

- Start documentation website

```bash
pnpm docs dev
```

- Run tests

```bash
pnpm test
```

## Recommended Extensions

We recommend using the following extensions in your editor:

- [ESLint](https://eslint.org/)
- [Prettier](https://prettier.io/)
- [EditorConfig](https://editorconfig.org/)
- [MDX](https://mdxjs.com/)
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/get-started/environments/iframe.mdx

```text
---
title: Using Chakra UI in Iframe
description: A guide for installing and using Chakra UI in an Iframe
---

Iframes are useful for isolating styles and logic in a separate context. For
example, you might want to showcase a Chakra component in dedicated sandbox.

## Template

Use the following template to get started quickly

:::card-group

<ResourceCard
  type="github"
  title="Iframe template"
  url="https://github.com/chakra-ui/chakra-ui/tree/main/sandbox/iframe"
/>

:::

## Installation

> The minimum node version required is Node.20.x

:::steps

### Install dependencies

```bash
npm i @chakra-ui/react @emotion/react @emotion/cache react-frame-component
```

The additional packages used are:

- `react-frame-component` used to create an iframe easily
- `@emotion/cache` used to create a custom insertion point for styles

### Add snippets

Snippets are pre-built components that you can use to build your UI faster.
Using the `@chakra-ui/cli` you can add snippets to your project.

```bash
npx @chakra-ui/cli snippet add
```

### Update tsconfig

If you're using TypeScript, you need to update the `compilerOptions` in the
tsconfig file to include the following options:

```json
{
  "compilerOptions": {
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "skipLibCheck": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

### Setup Iframe

Create a `components/ui/iframe-provider.tsx` file to setup the iframe using the
`react-frame-component` package.

```tsx title="components/ui/iframe-provider.tsx"
import {
  ChakraProvider,
  EnvironmentProvider,
  defaultSystem,
} from "@chakra-ui/react"
import createCache from "@emotion/cache"
import { CacheProvider } from "@emotion/react"
import Iframe, { FrameContextConsumer } from "react-frame-component"

function memoize<T extends object, R>(func: (arg: T) => R): (arg: T) => R {
  const cache = new WeakMap<T, R>()
  return (arg: T) => {
    if (cache.has(arg)) return cache.get(arg)!
    const ret = func(arg)
    cache.set(arg, ret)
    return ret
  }
}

const createCacheFn = memoize((container: HTMLElement) =>
  createCache({ container, key: "frame" }),
)

export const IframeProvider = (props: React.PropsWithChildren) => {
  const { children } = props
  return (
    <Iframe>
      <FrameContextConsumer>
        {(frame) => {
          const head = frame.document?.head
          if (!head) return null
          return (
            <CacheProvider value={createCacheFn(head)}>
              <EnvironmentProvider value={() => head.ownerDocument}>
                <ChakraProvider value={defaultSystem}>
                  {children}
                </ChakraProvider>
              </EnvironmentProvider>
            </CacheProvider>
          )
        }}
      </FrameContextConsumer>
    </Iframe>
  )
}
```

### Setup provider

Wrap your application with the `Provider` component generated in the
`components/ui/provider` component at the root of your application.

This provider composes the following:

- `ChakraProvider` from `@chakra-ui/react` for the styling system
- `ThemeProvider` from `next-themes` for color mode

```jsx
import { Provider } from "@/components/ui/provider"
import React from "react"
import ReactDOM from "react-dom/client"
import App from "./App"

ReactDOM.createRoot(document.getElementById("root")!).render(
  <React.StrictMode>
    <Provider>
      <App />
    </Provider>
  </React.StrictMode>,
)
```

### Use the IframeProvider

At any component in your application, wrap it with the `IframeProvider`
component to render it inside the iframe.

```tsx title="src/App.tsx"
import { Button, Container, Heading, Stack } from "@chakra-ui/react"
import { IframeProvider } from "./components/ui/iframe-provider"

function App() {
  return (
    <Container py="8">
      <Heading mb="5">Outside Iframe</Heading>

      <IframeProvider>
        <Stack p="6" align="flex-start" border="1px solid red">
          <Heading>Inside Iframe</Heading>
          <Button>Click me</Button>
        </Stack>
      </IframeProvider>
    </Container>
  )
}

export default App
```

:::

## Customization

If you created a custom theme using the `createSystem` function, ensure it's
passed to the `IframeProvider` and `Provider` components to ensure it's used
inside the iframe.

For example, let's say you created a custom theme:

```ts
export const system = createSystem(defaultConfig, {
  theme: { colors: {} },
})
```

Then, pass it to the `IframeProvider` and `Provider` components:

```tsx
<ChakraProvider value={system}>{/* ... */}</ChakraProvider>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/get-started/environments/shadow-dom.mdx

```text
---
title: Using Chakra UI in Shadow DOM
description: A guide for installing Chakra UI with Shadow DOM
---

When developing extensions for browsers or using Chakra as part of a large
project, leveraging the Shadow DOM is useful for style and logic encapsulation.

## Template

Use the following template to get started quickly

:::card-group

<ResourceCard
  type="github"
  title="Shadow DOM template"
  url="https://github.com/chakra-ui/chakra-ui/tree/main/sandbox/shadow-dom"
/>

:::

## Installation

> The minimum node version required is Node.20.x

:::steps

### Install dependencies

```bash
npm i @chakra-ui/react @emotion/react @emotion/cache react-shadow
```

The additional packages used are:

- `react-shadow` used to create a Shadow DOM easily
- `@emotion/cache` used to create a custom insertion point for styles

### Add snippets

Snippets are pre-built components that you can use to build your UI faster.
Using the `@chakra-ui/cli` you can add snippets to your project.

```bash
npx @chakra-ui/cli snippet add
```

### Update tsconfig

If you're using TypeScript, you need to update the `compilerOptions` in the
tsconfig file to include the following options:

```json
{
  "compilerOptions": {
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "skipLibCheck": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

### Configure style engine

Create a `system.ts` file in the root of your project and configure the style
engine.

```tsx title="components/ui/system.ts"
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const varRoot = ":host"

const config = defineConfig({
  cssVarsRoot: varRoot,
  conditions: {
    light: `${varRoot} &, .light &`,
  },
  preflight: { scope: varRoot },
  globalCss: {
    [varRoot]: defaultConfig.globalCss?.html ?? {},
  },
})

export const system = createSystem(defaultConfig, config)
```

> **Good to know**: The main purpose of the `system.ts` file is to configure the
> style engine to target the Shadow DOM.

### Setup provider

Update the generated `components/ui/provider` component with the `Provider`
component.

This provider composes the following:

- `ChakraProvider` from `@chakra-ui/react` for the styling system
- `EnvironmentProvider` from `react-shadow` to ensure Chakra components query
  the DOM correctly
- `CacheProvider` from `@emotion/react` to provide the custom insertion point
- `ThemeProvider` from `next-themes` for color mode

```tsx title="components/ui/provider.tsx"
"use client"

import { ChakraProvider, EnvironmentProvider } from "@chakra-ui/react"
import createCache from "@emotion/cache"
import { CacheProvider } from "@emotion/react"
import { ThemeProvider, type ThemeProviderProps } from "next-themes"
import { useEffect, useState } from "react"
import root from "react-shadow/emotion"
import { system } from "./system"

export function Provider(props: ThemeProviderProps) {
  const [shadow, setShadow] = useState<HTMLElement | null>(null)
  const [cache, setCache] = useState<ReturnType<typeof createCache> | null>(
    null,
  )

  useEffect(() => {
    if (!shadow?.shadowRoot || cache) return
    const emotionCache = createCache({
      key: "root",
      container: shadow.shadowRoot,
    })
    setCache(emotionCache)
  }, [shadow, cache])

  return (
    <root.div ref={setShadow}>
      {shadow && cache && (
        <EnvironmentProvider value={() => shadow.shadowRoot ?? document}>
          <CacheProvider value={cache}>
            <ChakraProvider value={system}>
              <ThemeProvider {...props} />
            </ChakraProvider>
          </CacheProvider>
        </EnvironmentProvider>
      )}
    </root.div>
  )
}
```

### Use the provider

Wrap your application with the `Provider` component generated in the
`components/ui/provider` component at the root of your application.

```tsx title="src/main.tsx" {1,8,10}
import { Provider } from "@/components/ui/provider"
import { StrictMode } from "react"
import { createRoot } from "react-dom/client"
import App from "./App.tsx"

createRoot(document.getElementById("root")!).render(
  <StrictMode>
    <Provider>
      <App />
    </Provider>
  </StrictMode>,
)
```

### Enjoy!

With the power of the snippets and the primitive components from Chakra UI, you
can build your UI faster.

```tsx
import { Button } from "@/components/ui/button"
import { HStack } from "@chakra-ui/react"

export default function App() {
  return (
    <HStack>
      <Button>Click me</Button>
      <Button>Click me</Button>
    </HStack>
  )
}
```

:::
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/get-started/frameworks/next-app.mdx

```text
---
title: Using Chakra UI in Next.js (App)
description: A guide for installing Chakra UI with Next.js app directory
---

## Templates

Use one of the following templates to get started quickly. The templates are
configured correctly to use Chakra UI.

:::card-group

<ResourceCard
  type="github"
  title="Next.js app template"
  url="https://github.com/chakra-ui/chakra-ui/tree/main/sandbox/next-app"
/>

<ResourceCard
  type="github"
  title="Next.js pages template"
  url="https://github.com/chakra-ui/chakra-ui/tree/main/sandbox/next-pages"
/>

:::

## Installation

> The minimum node version required is Node.20.x

:::steps

### Install dependencies

```bash
npm i @chakra-ui/react @emotion/react
```

### Add snippets

Snippets are pre-built components that you can use to build your UI faster.
Using the `@chakra-ui/cli` you can add snippets to your project.

```bash
npx @chakra-ui/cli snippet add
```

### Update tsconfig

If you're using TypeScript, you need to update the `compilerOptions` in the
tsconfig file to include the following options:

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "skipLibCheck": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

> If you're using JavaScript, create a `jsconfig.json` file and add the above
> code to the file.

### Setup provider

Wrap your application with the `Provider` component generated in the
`components/ui/provider` component at the root of your application.

This provider composes the following:

- `ChakraProvider` from `@chakra-ui/react` for the styling system
- `ThemeProvider` from `next-themes` for color mode

```tsx title="app/layout.tsx" {1,6,8}
import { Provider } from "@/components/ui/provider"

export default function RootLayout(props: { children: React.ReactNode }) {
  const { children } = props
  return (
    <html suppressHydrationWarning>
      <body>
        <Provider>{children}</Provider>
      </body>
    </html>
  )
}
```

> Adding the `suppressHydrationWarning` prop to the `html` element is required
> to prevent the warning about the `next-themes` library.

### Optimize Bundle

We recommend using the `experimental.optimizePackageImports` feature in Next.js
to optimize your bundle size by loading only the modules that you are actually
using.

```tsx title="next.config.mjs" {3}
export default {
  experimental: {
    optimizePackageImports: ["@chakra-ui/react"],
  },
}
```

This also helps to resolve warnings like:

```sh
[webpack.cache.PackFileCacheStrategy] Serializing big strings (xxxkiB)
```

### Enjoy!

With the power of the snippets and the primitive components from Chakra UI, you
can build your UI faster.

```tsx
import { Button } from "@/components/ui/button"
import { HStack } from "@chakra-ui/react"

const Demo = () => {
  return (
    <HStack>
      <Button>Click me</Button>
      <Button>Click me</Button>
    </HStack>
  )
}
```

:::
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/get-started/frameworks/next-pages.mdx

```text
---
title: Using Chakra UI in Next.js (Pages)
description: A guide for installing Chakra UI with Next.js pages directory
---

## Templates

Use one of the following templates to get started quickly. The templates are
configured correctly to use Chakra UI.

:::card-group

<ResourceCard
  type="github"
  title="Next.js app template"
  url="https://github.com/chakra-ui/chakra-ui/tree/main/sandbox/next-app"
/>

<ResourceCard
  type="github"
  title="Next.js pages template"
  url="https://github.com/chakra-ui/chakra-ui/tree/main/sandbox/next-pages"
/>

:::

## Installation

> The minimum node version required is Node.20.x

:::steps

### Install dependencies

```bash
npm i @chakra-ui/react @emotion/react
```

### Add snippets

Snippets are pre-built components that you can use to build your UI faster.
Using the `@chakra-ui/cli` you can add snippets to your project.

```bash
npx @chakra-ui/cli snippet add
```

### Update tsconfig

If you're using TypeScript, you need to update the `compilerOptions` in the
tsconfig file to include the following options:

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "skipLibCheck": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

> If you're using JavaScript, create a `jsconfig.json` file and add the above
> code to the file.

### Setup provider

Wrap your application with the `Provider` component at the root of your
application.

This provider composes the following:

- `ChakraProvider` from `@chakra-ui/react` for the styling system
- `ThemeProvider` from `next-themes` for color mode

```tsx title="pages/_app.tsx" {1,5,7}
import { Provider } from "@/components/ui/provider"

export default function App({ Component, pageProps }: AppProps) {
  return (
    <Provider>
      <Component {...pageProps} />
    </Provider>
  )
}
```

In the `pages/_document.tsx` file, add the `suppressHydrationWarning` prop to
the `html` element.

```tsx title="pages/_document.tsx" {5}
import { Head, Html, Main, NextScript } from "next/document"

export default function Document() {
  return (
    <Html suppressHydrationWarning>
      <Head />
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```

### Optimize Bundle

We recommend using the `experimental.optimizePackageImports` feature in Next.js
to optimize your bundle size by loading only the modules that you are actually
using.

```tsx title="next.config.mjs" {3}
export default {
  experimental: {
    optimizePackageImports: ["@chakra-ui/react"],
  },
}
```

This also helps to resolve warnings like:

```sh
[webpack.cache.PackFileCacheStrategy] Serializing big strings (xxxkiB)
```

### Enjoy!

With the power of the snippets and the primitive components from Chakra UI, you
can build your UI faster.

```tsx
import { Button } from "@/components/ui/button"
import { HStack } from "@chakra-ui/react"

const Demo = () => {
  return (
    <HStack>
      <Button>Click me</Button>
      <Button>Click me</Button>
    </HStack>
  )
}
```

:::
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/get-started/frameworks/remix.mdx

```text
---
title: Using Chakra in Remix
description: A guide for installing Chakra UI with Remix projects
---

## Templates

Use the remix template below to get started quickly.

:::card-group

<ResourceCard
  type="github"
  title="Remix template"
  url="https://github.com/chakra-ui/chakra-ui/tree/main/sandbox/remix-ts"
/>

:::

## Installation

:::steps

### Install dependencies

```bash
npm i @chakra-ui/react @emotion/react
```

### Add snippets

Snippets are pre-built components that you can use to build your UI faster.
Using the `@chakra-ui/cli` you can add snippets to your project.

```bash
npx @chakra-ui/cli snippet add
```

### Setup emotion cache

Using snippets from the Remix sandbox, you can add the emotion cache to your
application.

[Emotion cache snippet](https://github.com/chakra-ui/chakra-ui/blob/main/sandbox/remix-ts/app/emotion)

### Update tsconfig

If you're using TypeScript, you need to update the `compilerOptions` in the
tsconfig file to include the following options:

```json title="tsconfig.json"
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "skipLibCheck": true
  }
}
```

> If you're using JavaScript, create a `jsconfig.json` file and add the above
> code to the file.

### Setup provider

Wrap your application with the `Provider` component at the root of your
application.

This provider composes the following:

- `ChakraProvider` from `@chakra-ui/react` for the styling system
- `ThemeProvider` from `next-themes` for color mode

```tsx title="app/root.tsx"
import React from "react"
import ReactDOM from "react-dom/client"
import App from "./App"
import { Provider } from "@/components/ui/provider"

export default function App() {
  return (
    <Provider>
      <Outlet />
    </Provider>
  )
}
```

### Enjoy!

When the power of the snippets and the primitive components from Chakra UI, you
can build your UI faster.

```tsx
import { Button } from "@/components/ui/button"
import { HStack } from "@chakra-ui/react"

const Demo = () => {
  return (
    <HStack>
      <Button>Click me</Button>
      <Button>Click me</Button>
    </HStack>
  )
}
```

:::

## Known issues

You may encounter the following issues when using Chakra UI with Remix:

```bash
Error: There was an error while hydrating. Because the error happened outside of a Suspense boundary, the entire root will switch to client rendering.
```

This is a known issue related to extension installed in your browser. We
recommend testing your application in incognito mode to see if the issue
persists.

> We welcome contributions to fix this issue.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/get-started/frameworks/storybook.mdx

```text
---
title: Using Chakra UI in Storybook
description: A guide for using Chakra UI with Storybook
---

## Installation

:::steps

### Install dependencies

Install the required dependencies for Chakra UI and Storybook.

```bash
npm i @storybook/addon-themes @chakra-ui/react @emotion/react
```

### Setup Preview

Edit the `.storybook/preview.tsx` file to include the Chakra UI provider.

```tsx
import { ChakraProvider, defaultSystem } from "@chakra-ui/react"
import type { Preview } from "@storybook/react"

const preview: Preview = {
  // ...
  decorators: [
    (Story) => (
      <ChakraProvider value={defaultSystem}>
        <Story />
      </ChakraProvider>
    ),
  ],
}

export default preview
```

### Setup dark mode toggle

Use the `withThemeByClassName` decorator from `@storybook/addon-themes` to add a
color mode toggle to the Storybook toolbar.

```tsx
import { withThemeByClassName } from "@storybook/addon-themes"
import type { Preview, ReactRenderer } from "@storybook/react"

const preview: Preview = {
  decorators: [
    // ...
    withThemeByClassName({
      defaultTheme: "light",
      themes: { light: "", dark: "dark" },
    }),
  ],
}

export default preview
```

### Start the Storybook server

```bash
npm run storybook
```

### Enjoy!

Use Chakra UI components in your stories.

```tsx
import { Button } from "@chakra-ui/react"

export const SampleStory = {
  render() {
    return <Button>Click me</Button>
  },
}
```

:::
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/get-started/frameworks/vite.mdx

```text
---
title: Using Chakra in Vite
description: A guide for installing Chakra UI with Vite.js projects
---

## Templates

Use the vite template below to get started quickly.

:::card-group

<ResourceCard
  type="github"
  title="Vite template"
  url="https://github.com/chakra-ui/chakra-ui/tree/main/sandbox/vite-ts"
/>

:::

## Installation

> The minimum node version required is Node.20.x

:::steps

### Install dependencies

```bash
npm i @chakra-ui/react @emotion/react
```

### Add snippets

Snippets are pre-built components that you can use to build your UI faster.
Using the `@chakra-ui/cli` you can add snippets to your project.

```bash
npx @chakra-ui/cli snippet add
```

### Update tsconfig

If you're using TypeScript, in the `tsconfig.app.json` file, make sure the
`compilerOptions` includes the following:

```json title="tsconfig.app.json"
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "skipLibCheck": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

> If you're using JavaScript, create a `jsconfig.json` file and add the above
> code to the file.

### Setup provider

Wrap your application with the `Provider` component at the root of your
application.

This provider composes the following:

- `ChakraProvider` from `@chakra-ui/react` for the styling system
- `ThemeProvider` from `next-themes` for color mode

```tsx title="src/main.tsx" {1,8,10}
import { Provider } from "@/components/ui/provider"
import React from "react"
import ReactDOM from "react-dom/client"
import App from "./App"

ReactDOM.createRoot(document.getElementById("root")!).render(
  <React.StrictMode>
    <Provider>
      <App />
    </Provider>
  </React.StrictMode>,
)
```

### Setup Vite Config Paths

In your project, set up a vite config path to automatically sync `tsconfig` with
vite using the command:

```bash
npm i -D vite-tsconfig-paths
```

Update the `vite.config.ts` file:

```ts {3} /tsconfigPaths()/
import react from "@vitejs/plugin-react"
import { defineConfig } from "vite"
import tsconfigPaths from "vite-tsconfig-paths"

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react(), tsconfigPaths()],
})
```

### Enjoy!

With the power of the snippets and the primitive components from Chakra UI, you
can build your UI faster.

```tsx
import { Button } from "@/components/ui/button"
import { HStack } from "@chakra-ui/react"

const Demo = () => {
  return (
    <HStack>
      <Button>Click me</Button>
      <Button>Click me</Button>
    </HStack>
  )
}
```

:::
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/get-started/installation.mdx

```text
---
title: Installation
description: How to install and set up Chakra UI in your project
---

## Framework Guide

Chakra UI works in your favorite framework. We've put together step-by-step
guides for these frameworks

:::card-group

<Card title="Next.js" href="/docs/get-started/frameworks/next-app" icon="nextjs">

Easily add Chakra UI with Next.js app

</Card>

<Card title="Vite" href="/docs/get-started/frameworks/vite" icon="vite">

Use Chakra UI in with Vite

</Card>

<Card title="Stackblitz" href="https://stackblitz.com/edit/chakra-ui-v3" icon="stackblitz">

Try Chakra UI in Stackblitz sandbox

</Card>

:::

> The minimum node version required is Node.20.x

## Installation

To manually set up Chakra UI in your project, follow the steps below.

:::steps

### Install `@chakra-ui/react`

```bash
npm i @chakra-ui/react @emotion/react
```

### Add snippets

Snippets are pre-built components that you can use to build your UI faster.
Using the `@chakra-ui/cli` you can add snippets to your project.

```bash
npx @chakra-ui/cli snippet add
```

### Setup provider

Wrap your application with the `Provider` component generated in the
`components/ui/provider` component at the root of your application.

This provider composes the following:

- `ChakraProvider` from `@chakra-ui/react` for the styling system
- `ThemeProvider` from `next-themes` for color mode

```jsx
import { Provider } from "@/components/ui/provider"

function App({ Component, pageProps }) {
  return (
    <Provider>
      <Component {...pageProps} />
    </Provider>
  )
}
```

### Update tsconfig

If you're using TypeScript, you need to update the `compilerOptions` in the
tsconfig file to include the following options:

```json
{
  "compilerOptions": {
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "skipLibCheck": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

> If you're using JavaScript, create a `jsconfig.json` file and add the above
> code to the file.

### Enjoy!

With the power of the snippets and the primitive components from Chakra UI, you
can build your UI faster.

```tsx
import { Button } from "@/components/ui/button"
import { HStack } from "@chakra-ui/react"

const Demo = () => {
  return (
    <HStack>
      <Button>Click me</Button>
      <Button>Click me</Button>
    </HStack>
  )
}
```

:::

## Learn

Watch our official courses and dive into dozens of videos that will teach you
everything you need to know about Chakra UI, from basics to advanced concepts.

## Contribute

Whether you're a beginner or advanced Chakra UI user, joining our community is
the best way to connect with like-minded people who build great products with
the library.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/get-started/migration.mdx

```text
---
title: Migration to v3
description: How to migrate to Chakra UI v3.x from v2.x
---

:::warning

This guide is a work in progress and will be updated from time to time. If you
find any point we missed, feel free to open a PR to update this guide.

:::

## Steps

> The minimum node version required is Node.20.x

:::steps

### Update Packages

Remove the unused packages: `@emotion/styled` and `framer-motion`. These
packages are no longer required in Chakra UI.

```bash
npm uninstall @emotion/styled framer-motion
```

Install updated versions of the packages: `@chakra-ui/react` and
`@emotion/react`.

```bash
npm install @chakra-ui/react@latest @emotion/react@latest
```

Next, install component snippets using the CLI snippets. Snippets provide
pre-built compositions of Chakra components to save you time and put you in
charge.

```bash
npx @chakra-ui/cli snippet add
```

### Refactor Custom Theme

Move your custom theme to a dedicated `theme.js` or `theme.ts` file. Use
`createSystem` and `defaultConfig` to configure your theme.

**Before**

```ts
import { extendTheme } from "@chakra-ui/react"

export const theme = extendTheme({
  fonts: {
    heading: `'Figtree', sans-serif`,
    body: `'Figtree', sans-serif`,
  },
})
```

**After**

```ts {3}
import { createSystem, defaultConfig } from "@chakra-ui/react"

export const system = createSystem(defaultConfig, {
  theme: {
    tokens: {
      fonts: {
        heading: { value: `'Figtree', sans-serif` },
        body: { value: `'Figtree', sans-serif` },
      },
    },
  },
})
```

> All token values need to be wrapped in an object with a **value** key. Learn
> more about tokens [here](/docs/theming/tokens).

### Update ChakraProvider

Update the ChakraProvider import from `@chakra-ui/react` to the one from the
snippets. Next, rename the `theme` prop to `value` to match the new system-based
theming approach.

**Before**

```tsx
import { ChakraProvider } from "@chakra-ui/react"

export const App = ({ Component }) => (
  <ChakraProvider theme={theme}>
    <Component />
  </ChakraProvider>
)
```

**After**

```tsx {1,3}
import { Provider } from "@/components/ui/provider"
import { defaultSystem } from "@chakra-ui/react"

export const App = ({ Component }) => (
  <Provider value={defaultSystem}>
    <Component />
  </Provider>
)
```

> If you have a custom theme, replace `defaultSystem` with the custom `system`

The Provider component compose the `ChakraProvider` from Chakra and
`ThemeProvider` from `next-themes`

:::

## Improvements

- **Performance:** Improved reconciliation performance by `4x` and re-render
  performance by `1.6x`

- **Namespaced imports:** Import components using the dot notation for more
  concise imports

  ```tsx
  import { Accordion } from "@chakra-ui/react"

  const Demo = () => {
    return (
      <Accordion.Root>
        <Accordion.Item>
          <Accordion.ItemTrigger />
          <Accordion.ItemContent />
        </Accordion.Item>
      </Accordion.Root>
    )
  }
  ```

- **TypeScript:** Improved IntelliSense and type inference for style props and
  tokens.

- **Polymorphism:** Loosened the `as` prop typings in favor of using the
  `asChild` prop. This pattern was inspired by Radix Primitives and Ark UI.

## Removed Features

### Color Mode

- `ColorModeProvider` and `useColorMode` have been removed in favor of
  `next-themes`
- `LightMode`, `DarkMode` and `ColorModeScript` components have been removed.
  You now have to use `className="light"` or `className="dark"` to force themes.
- `useColorModeValue` has been removed in favor of `useTheme` from `next-themes`

:::note

We provide snippets for color mode via the CLI to help you set up color mode
quickly using `next-themes`

:::

### Hooks

We removed the hooks package in favor of using dedicated, robust libraries like
`react-use` and `usehooks-ts`

The only hooks we ship now are `useBreakpointValue`, `useCallbackRef`,
`useDisclosure`, `useControllableState` and `useMediaQuery`.

### Style Config

We removed the `styleConfig` and `multiStyleConfig` concept in favor of recipes
and slot recipes. This pattern was inspired by Panda CSS.

### Next.js package

We've removed the `@chakra-ui/next-js` package in favor of using the `asChild`
prop for better flexibility.

To style the Next.js image component, use the `asChild` prop on the `Box`
component.

```jsx
<Box asChild>
  <NextImage />
</Box>
```

To style the Next.js link component, use the `asChild` prop on the

```jsx
<Link isExternal asChild>
  <NextLink />
</Link>
```

### Theme Tools

We've removed this package in favor using CSS color mix.

**Before**

We used JS to resolve the colors and then apply the transparency

```jsx
defineStyle({
  bg: transparentize("blue.200", 0.16)(theme),
  // -> rgba(0, 0, 255, 0.16)
})
```

**After**

We now use CSS color-mix

```jsx
defineStyle({
  bg: "blue.200/16",
  // -> color-mix(in srgb, var(--chakra-colors-200), transparent 16%)
})
```

### forwardRef

Due to the simplification of the `as` prop, we no longer provide a custom
`forwardRef`. Prefer to use `forwardRef` from React directly.

### Icons

Removed `@chakra-ui/icons` package. Prefer to use `lucide-react` or
`react-icons` instead.

### Storybook Addon

We're removed the storybook addon in favor of using `@storybook/addon-themes`
and `withThemeByClassName` helper.

```tsx
import { ChakraProvider, defaultSystem } from "@chakra-ui/react"
import { withThemeByClassName } from "@storybook/addon-themes"
import type { Preview, ReactRenderer } from "@storybook/react"

const preview: Preview = {
  decorators: [
    withThemeByClassName<ReactRenderer>({
      defaultTheme: "light",
      themes: {
        light: "",
        dark: "dark",
      },
    }),
    (Story) => (
      <ChakraProvider value={defaultSystem}>
        <Story />
      </ChakraProvider>
    ),
  ],
}

export default preview
```

### Removed Components

- **Wrap**: Replace with `HStack` and add `wrap=wrap` to it.
- **WrapItem**: Replace with `Flex` and add `align=flex-start` to it.
- **StackItem**: You don't need this anymore. Use `Box` instead.
- **FocusLock**: We no longer ship a focus lock component. Use
  `react-focus-lock` instead.
- **FormControl** and **FormErrorMessage**: Replace with the `Field` component

## Prop Changes

### Boolean Props

Changed naming convention for boolean properties from `is<X>` to `<x>`

- `isOpen` -> `open`
- `defaultIsOpen` -> `defaultOpen`
- `isDisabled` -> `disabled`
- `isInvalid` -> `invalid`
- `isRequired` -> `required`

### ColorScheme Prop

The `colorScheme` prop has been changed to `colorPalette`

**Before**

- You could only use `colorScheme` in a component's theme
- `colorScheme` clashes with the native `colorScheme` prop in HTML elements

```tsx
<Button colorScheme="blue">Click me</Button>
```

**After**

- You can now use `colorPalette` anywhere

```tsx
<Button colorPalette="blue">Click me</Button>
```

Usage in any component, you can do something like:

```tsx
<Box colorPalette="red">
  <Box bg="colorPalette.400">Some box</Box>
  <Text color="colorPalette.600">Some text</Text>
</Box>
```

If you are using custom colors, you must define two things to make
`colorPalette` work:

- **tokens**: For the 50-950 color palette
- **semanticTokens**: For the `solid`, `contrast`, `fg`, `muted`, `subtle`,
  `emphasized`, and `focusRing` color keys

```tsx title="theme.ts" /brand: {/ /tokens: {/ /semanticTokens: {/
import { createSystem, defaultConfig } from "@chakra-ui/react"

export const system = createSystem(defaultConfig, {
  theme: {
    tokens: {
      colors: {
        brand: {
          50: { value: "#e6f2ff" },
          100: { value: "#e6f2ff" },
          200: { value: "#bfdeff" },
          300: { value: "#99caff" },
          // ...
          950: { value: "#001a33" },
        },
      },
    },
    semanticTokens: {
      colors: {
        brand: {
          solid: { value: "{colors.brand.500}" },
          contrast: { value: "{colors.brand.100}" },
          fg: { value: "{colors.brand.700}" },
          muted: { value: "{colors.brand.100}" },
          subtle: { value: "{colors.brand.200}" },
          emphasized: { value: "{colors.brand.300}" },
          focusRing: { value: "{colors.brand.500}" },
        },
      },
    },
  },
})
```

> Read more about it [here](/guides/theming-custom-colors).

### Gradient Props

Gradient style prop simplified to `gradient` and `gradientFrom` and `gradientTo`
props. This reduces the runtime performance cost of parsing the gradient string,
and allows for better type inference.

**Before**

```tsx
<Box bgGradient="linear(to-r, red.200, pink.500)" />
```

**After**

```tsx
<Box bgGradient="to-r" gradientFrom="red.200" gradientTo="pink.500" />
```

### Color Palette

- Default color palette is now `gray` for all components but you can configure
  this in your theme.

- Default theme color palette size has been increased to 11 shades to allow more
  color variations.

  **Before**

  ```tsx
  const colors = {
    // ...
    gray: {
      50: "#F7FAFC",
      100: "#EDF2F7",
      200: "#E2E8F0",
      300: "#CBD5E0",
      400: "#A0AEC0",
      500: "#718096",
      600: "#4A5568",
      700: "#2D3748",
      800: "#1A202C",
      900: "#171923",
    },
  }
  ```

  **After**

  ```tsx
  const colors = {
    // ...
    gray: {
      50: { value: "#fafafa" },
      100: { value: "#f4f4f5" },
      200: { value: "#e4e4e7" },
      300: { value: "#d4d4d8" },
      400: { value: "#a1a1aa" },
      500: { value: "#71717a" },
      600: { value: "#52525b" },
      700: { value: "#3f3f46" },
      800: { value: "#27272a" },
      900: { value: "#18181b" },
      950: { value: "#09090b" },
    },
  }
  ```

### Style Props

Changed the naming convention for some style props

- `noOfLines` -> `lineClamp`
- `truncated` -> `truncate`
- `_activeLink` -> `_currentPage`
- `_activeStep` -> `_currentStep`
- `_mediaDark` -> `_osDark`
- `_mediaLight` -> `_osLight`

We removed the `apply` prop in favor of `textStyle` or `layerStyles`

### Nested Styles

We have changed the way you write nested styles in Chakra UI components.

**Before**

Write nested styles using the `sx` or `__css` prop, and you sometimes don't get
auto-completion for nested styles.

```tsx
<Box
  sx={{
    svg: { color: "red.500" },
  }}
/>
```

**After**

Write nested styles using the `css` prop. All nested selectors **require** the
use of the ampersand `&` prefix

```tsx
<Box
  css={{
    "& svg": { color: "red.500" },
  }}
/>
```

This was done for two reasons:

- **Faster style processing:** Before we had to check if a style key is a style
  prop or a selector which is quite expensive overall.
- **Better typings:** This makes it easier to type nested style props are
  strongly typed

## Component Changes

### ChakraProvider

- Removed `theme` prop in favor of passing the `system` prop instead. Import the
  `defaultSystem` module instead of `theme`

- Removed `resetCss` prop in favor of passing `preflight: false` to the
  `createSystem` function

Before

```tsx
<ChakraProvider resetCss={false}>
  <Component />
</ChakraProvider>
```

After

```tsx
const system = createSystem(defaultConfig, { preflight: false })

<Provider value={system}>
  <Component />
</Provider>
```

- Removed support for configuring toast options. Pass it to the `createToaster`
  function in `components/ui/toaster.tsx` file instead.

### Modal

- Renamed to `Dialog`
- Remove `isCentered` prop in favor of using the `placement=center` prop
- Removed `isOpen` and `onClose` props in favor of using the `open` and
  `onOpenChange` props

### Avatar

- Remove `max` prop in favor of userland control
- Remove excess label part
- Move image related props to `Avatar.Image` component
- Move fallback icon to `Avatar.Fallback` component
- Move `name` prop to `Avatar.Fallback` component

### Portal

- Remove `appendToParentPortal` prop in favor of using the `containerRef`
- Remove `PortalManager` component

### Stack

- Changed `spacing` to `gap`
- Removed `StackItem` in favor of using the `Box` component directly

### Collapse

- Rename `Collapse` to `Collapsible` namespace
- Rename `in` to `open`
- `animateOpacity` has been removed, use keyframes animations `expand-height`
  and `collapse-height` instead

Before

```tsx
<Collapse in={isOpen} animateOpacity>
  Some content
</Collapse>
```

After

```tsx
<Collapsible.Root open={isOpen}>
  <Collapsible.Content>Some content</Collapsible.Content>
</Collapsible.Root>
```

### Image

- Now renders a native `img` without any fallback
- Remove `fallbackSrc` due to the SSR issues it causes
- Remove `useImage` hook
- Remove `Img` in favor of using the `Image` component directly

### PinInput

- Changed `value`, `defaultValue` and `onChange` to use `string[]` instead of
  `string`
- Add new `PinInput.Control` and `PinInput.Label` component parts
- `PinInput.Root` now renders a `div` element by default. Consider combining
  with `Stack` or `Group` for better layout control

### NumberInput

- Rename `NumberInputStepper` to `NumberInput.Control`
- Rename `NumberInputStepperIncrement` to `NumberInput.IncrementTrigger`
- Rename `NumberInputStepperDecrement` to `NumberInput.DecrementTrigger`
- Remove `focusBorderColor` and `errorBorderColor`, consider setting the
  `--focus-color` and `--error-color` css variables instead

Before

```tsx
<NumberInput>
  <NumberInputField />
  <NumberInputStepper>
    <NumberIncrementStepper />
    <NumberDecrementStepper />
  </NumberInputStepper>
</NumberInput>
```

After

```tsx
<NumberInput.Root>
  <NumberInput.Field />
  <NumberInput.Control>
    <NumberInput.IncrementTrigger />
    <NumberInput.DecrementTrigger />
  </NumberInput.Control>
</NumberInput.Root>
```

### Divider

- Rename to `Separator`
- Switch to `div` element for better layout control
- Simplify component to rely on `borderTopWidth` and `borderInlineStartWidth`
- To change the thickness reliably, set the `--divider-border-width` css
  variable

### Input, Select, Textarea

- Removed `invalid` prop in favor of wrapping the component in a `Field`
  component. This allows for adding a label, error text and asterisk easily.

Before

```tsx
<Input invalid />
```

After

```tsx
<Field invalid label="Email" errorText="This field is required">
  <Input />
</Field>
```

### Link

- Removed `isExternal` prop in favor of explicitly setting the `target` and
  `rel` props

Before

```tsx
<Link isExternal>Click me</Link>
```

After

```tsx
<Link target="_blank" rel="noopener noreferrer">
  Click me
</Link>
```

### IconButton

- Removed `icon` prop in favor of rendering the `children` prop directly
- Removed `isRounded` in favor of using the `borderRadius=full` prop
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/animation-styles.mdx

```text
---
title: Animation Styles
description:
  Learn how to use animation styles to define reusable motion properties.
---

## Overview

Animation styles allow you to define reusable animation properties. The goal is
to reduce the amount of code needed to animate components.

The supported animation styles are:

- **Animation**: animation composition, delay, direction, duration, fill mode,
  iteration count, name, play state, timing function

- **Animation range**: animation range, start, end, timeline

- **Transform origin**: transform origin

## Defining animation styles

Animation styles are defined using the `defineAnimationStyles` function.

Here's an example of an animation style:

```js
import { defineAnimationStyles } from "@chakra-ui/react"

const animationStyles = defineAnimationStyles({
  bounceFadeIn: {
    value: {
      animationName: "bounce, fade-in",
      animationDuration: "1s",
      animationTimingFunction: "ease-in-out",
      animationIterationCount: "infinite",
    },
  },
})
```

## Built-in animation styles

Chakra UI provides a set of built-in animation styles that you can use.

<ExamplePreview name="tokens/animation-style" />

## Update the theme

To use the animation styles, update the `theme` object with the
`animationStyles` property.

```js filename="theme.ts"
import { createSystem, defineConfig } from "@chakra-ui/react"
import { animationStyles } from "./animation-styles"

const config = defineConfig({
  theme: {
    extend: {
      animationStyles,
    },
  },
})

export default createSystem(defaultConfig, config)
```

After updating the theme, run this command to generate the animations.

```bash
npx @chakra-ui/cli typegen
```

These animation styles can be composed with other styles like `_open` and
`_closed` which map to the `data-state=open|closed` attribute.

```jsx
<Box
  data-state="open"
  animationDuration="slow"
  animationStyle={{ _open: "slide-fade-in", _closed: "slide-fade-out" }}
>
  > This content will fade in
</Box>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/cascade-layers.mdx

```text
---
title: Cascade Layers
description:
  CSS cascade layers refer to the order in which CSS rules are applied to an
  HTML element.
---

Chakra UI relies on CSS cascade layers to provide a predictable, performant way
to override components. The layers are defined to match that of
[Panda CSS](https://panda-css.com).

> **Good to know**: This plays a major role in the faster reconciliation times
> in v3.x

## Layer Types

Chakra supports these cascade layer types:

- `@layer reset`: Where the preflight or css resets styles are defined.

- `@layer base`: Where global styles are placed when defined in `globalCss`
  config property.

- `@layer recipes`: Where styles for recipes are placed when defined in
  `theme.recipes` or `theme.slotRecipes`

- `@layer tokens`: Where styles for design tokens are placed when defined in
  `theme.tokens` or `theme.semanticTokens`

## Layer Order

Chakra appends the following layers to the top of the generated emotion
stylesheet:

```css
@layer reset, base, tokens, recipes;
```

This structure allows for smoother experience when combining Chakra and Panda
CSS in the same project.

## Disabling Layers

Cascade layers are enabled by default. If you want to disable them, you can do
so by setting the `disableLayers` option to `true`

```js title="theme.ts"
export const system = createSystem(defaultConfig, {
  disableLayers: true,
})
```

Next, edit the `components/ui/provider` file to use the new system

```tsx title="provider.tsx" {3} /value={system}/
import { ColorModeProvider } from "@/components/ui/color-mode"
import { ChakraProvider } from "@chakra-ui/react"
import { system } from "./theme"

export function Provider(props: React.PropsWithChildren) {
  return (
    <ChakraProvider value={system}>
      <ColorModeProvider>{props.children}</ColorModeProvider>
    </ChakraProvider>
  )
}
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/chakra-factory.mdx

```text
---
title: Chakra Factory
description: Use the chakra factory to create supercharged components
---

## Overview

Chakra factory serves as a way to create supercharged JSX component from any
HTML element to enable them receive JSX style props.

```js
import { chakra } from "@chakra-ui/react"
```

The chakra factory can be used in two ways: as a JSX element or as a factory
function.

## JSX Element

Style props are CSS properties that you can pass as props to your components.
With the JSX factory, you can use `chakra.<element>` syntax to create JSX
elements that support style props.

```jsx
import { chakra } from "@chakra-ui/react"

const Button = ({ children }) => (
  <chakra.button bg="blue.500" color="white" py="2" px="4" rounded="md">
    {children}
  </chakra.button>
)
```

## Factory function

Use the `chakra` function to convert native elements or custom components. The
key requirement is that the component **must** accept `className` as props.

```jsx
const Link = chakra("a")

function Example() {
  return <Link bg="red.200" href="https://chakra-ui.com" />
}
```

Another example with a custom component.

```jsx
import * as RadixScrollArea from "@radix-ui/react-scroll-area"

const ScrollArea = chakra(RadixScrollArea.Root)

function Example() {
  return (
    <ScrollArea>
      <RadixScrollArea.Viewport>
        <div>Hello</div>
      </RadixScrollArea.Viewport>
    </ScrollArea>
  )
}
```

### Attaching styles

Use the `chakra` function to attach styles or recipes to components.

```jsx
const Link = chakra("a", {
  base: {
    bg: "papayawhip",
    color: "red.500",
  },
})

// usage: <Link href="https://chakra-ui.com" />
```

### Attaching recipes

Here's an example of attaching a recipe to the component.

```jsx
const Card = chakra("div", {
  base: {
    shadow: "lg",
    rounded: "lg",
    bg: "white",
  },
  variants: {
    variant: {
      outline: {
        border: "1px solid",
        borderColor: "red.500",
      },
      solid: {
        bg: "red.500",
        color: "white",
      },
    },
  },
})

// usage: <Card variant="outline" />
```

### Forwarding props

By default, the `chakra` factory only filters chakra related style props from
getting to the DOM. For more fine-grained control of how props are forwarded,
pass the `shouldForwardProp` option.

Here's an example that forwards all props that doesn't start with `$`

```jsx
const Component = chakra("div", {
  shouldForwardProp(prop) {
    return !prop.startsWith("$")
  },
})
```

To create custom forward props logic, combine the
[@emotion/is-prop-valid](https://github.com/emotion-js/emotion/tree/master/packages/is-prop-valid)
package and the `isValidProperty` from Chakra UI.

```tsx
import { chakra, defaultSystem } from "@chakra-ui/react"
import shouldForwardProp from "@emotion/is-prop-valid"

const { isValidProperty } = defaultSystem

const Component = chakra("div", {
  shouldForwardProp(prop, variantKeys) {
    const chakraSfp = !variantKeys?.includes(prop) && !isValidProperty(prop)
    return shouldForwardProp(prop) && chakraSfp
  },
})
```

## Default Props

Use the `defaultProps` option to pass default props to the component.

```jsx {9}
const Button = chakra(
  "button",
  {
    base: {
      bg: "blue.500",
      color: "white",
    },
  },
  { defaultProps: { type: "button" } },
)
```

## Polymorphism

Every component created with the chakra factory can accept the `as` and
`asChild` props to change the underlying DOM element.

```tsx
<Button as="a" href="https://chakra-ui.com">
  Chakra UI
</Button>
```

or

```tsx
<Button asChild>
  <a href="https://chakra-ui.com">Chakra UI</a>
</Button>
```

> Learn more about composition in Chakra UI
> [here](/docs/components/overview/composition)
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/color-opacity-modifier.mdx

```text
---
title: Color opacity modifier
description: How to dynamically set the opacity of a raw color or color token
---

Every color related style property can use the
[`color-mix`](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/color-mix)
shortcut to apply opacity to a color.

## Syntax

The general syntax is `{color}/{opacity}`. For example: `bg="red.300/40"`.

## Usage

```tsx
<Text bg="red.300/40" color="white">
  Hello World
</Text>
```

This will generate something like this:

```css {2,3}
.css-sxdf {
  --mix-background: color-mix(in srgb, var(--colors-red-300) 40%, transparent);
  background: var(--mix-background, var(--colors-red-300));
  color: var(--colors-white);
}
```

### CSS Variables

This feature can be used in css variables as well. This is useful for creating
one-off color token in a component.

The token reference syntax `{}` is required for this to work.

```tsx
<Box css={{ "--bg": "{colors.red.400/40}" }}>
  <Text>Hello World</Text>
  <Box bg="var(--bg)" />
</Box>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/conditional-styles.mdx

```text
---
title: Conditional Styles
description: Learn how to use conditional and responsive styles in Chakra.
---

## Overview

Chakra allows you to write styles for pseudo states, media queries, and custom
data attributes with the conditional style props.

:::note

See the list of [built-in conditions](#reference) below.

:::

## Usage

For example, here's how to change the background color of a button when it's
hovered:

```jsx
<Box bg="red.500" _hover={{ bg: "red.700" }}>
  Hover me
</Box>
```

### Nested condition

Conditional values can be nested to create complex selector rules.

Here's how to change the background color of an element when in focus on hover:

```jsx
<Box bg={{ base: "red.500", _hover: { _focus: "red.700" } }}>
  Hover & Focus me
</Box>
```

### At Rules

This also works with the supported at-rules (`@media`, `@layer`, `@container`,
`@supports`, and `@page`):

```tsx
<Box
  css={{
    "@container (min-width: 10px)": {
      color: "green.300",
    },
  }}
>
  Hello
</Box>
```

## Pseudo Classes

### Hover, Active, Focus, and Disabled

Here's an example of how to style the hover, active, focus, and disabled states
of an element

```jsx
<chakra.button
  _hover={{ bg: "red.700" }}
  _active={{ bg: "red.900" }}
  _focus={{ bg: "red.800" }}
  _disabled={{ opacity: "0.5" }}
>
  Hover me > Hover me
</chakra.button>
```

### First, Last, Odd, Even

Here's an example of how to style the first, last, odd, and even elements in a
list

```jsx
<Box as="ul">
  {items.map((item) => (
    <Box
      as="li"
      key={item}
      _first={{ color: "red.500" }}
      _last={{ color: "red.800" }}
    >
      {item}
    </Box>
  ))}
</Box>
```

You can also style even and odd elements using the `_even` and `_odd` modifier

```jsx
<table>
  <tbody>
    {items.map((item) => (
      <chakra.tr key={item} _even={{ bg: "gray.100" }} _odd={{ bg: "white" }}>
        <td>{item}</td>
      </chakra.tr>
    ))}
  </tbody>
</table>
```

## Pseudo Elements

### Before and After

To style the `::before` and `::after` pseudo elements of an element, use the
`_before` and `_after` modifiers

```jsx /_before/
<Box _before={{ content: '"👋"' }} _after={{ content: '"🥂"' }}>
  Hello
</Box>
```

### Placeholder

To style the placeholder text of any input or textarea, use the `_placeholder`
modifier:

```jsx {3}
<chakra.input
  placeholder="Enter your name"
  _placeholder={{ color: "gray.500" }}
/>
```

### File Inputs

To style the file input button, use the `_file` modifier:

```jsx {3}
<chakra.input
  type="file"
  _file={{ bg: "gray.500", px: "4", py: "2", marginEnd: "3" }}
/>
```

## Media Queries

### Reduced Motion

Use the `_motionReduce` and `_motionSafe` modifiers to style an element based on
the user's motion preference:

```jsx
<Box _motionSafe={{ transition: "all 0.3s" }}>Hello</Box>
```

### Color Scheme

The `prefers-color-scheme` media feature is used to detect if the user has
requested the system to use a light or dark color theme.

Use the `_osLight` and `_osDark` modifiers to style an element based on the
user's color scheme preference:

```jsx
<chakra.div bg={{ base: "white", _osDark: "black" }}>Hello</chakra.div>
```

### Color Contrast

The `prefers-contrast` media feature is used to detect if the user has requested
the system use a high or low contrast theme.

Use the `_highContrast` and `_lessContrast` modifiers to style an element based
on the user's color contrast preference:

```jsx
<Box bg={{ base: "white", _highContrast: "black" }}>Hello</Box>
```

### Orientation

The `orientation` media feature is used to detect if the user has a device in
portrait or landscape mode.

Use the `_portrait` and `_landscape` modifiers to style an element based on the
user's device orientation:

```jsx
<Box pb="4" _portrait={{ pb: "8" }}>
  Hello
</Box>
```

## Selectors

### Arbitrary selectors

For arbitrary, use the `css` prop to write styles for one-off selectors:

```tsx
<Box css={{ "&[data-state=closed]": { color: "red.300" } }} />
```

Here's another example that targets the child elements of a parent element:

```tsx
<Box
  css={{
    "& > *": { margin: "2" },
  }}
/>
```

### Group Selectors

To style an element based on its parent element's state or attribute, add the
`group` class to the parent element, and use any of the `_group*` modifiers on
the child element.

```jsx
<div className="group">
  <Text _groupHover={{ bg: "red.500" }}>Hover me</Text>
</div>
```

This modifier works for every pseudo class modifiers like `_groupHover`,
`_groupActive`, `_groupFocus`, and `_groupDisabled`, etc.

### Sibling Selectors

To style an element based on its sibling element's state or attribute, add the
`peer` class to the sibling element, and use any of the `_peer*` modifiers on
the target element.

```jsx /_peerHover={{ bg: "red.500" }}/
<div>
  <p className="peer">Hover me</p>
  <Box _peerHover={{ bg: "red.500" }}>I'll change by bg</Box>
</div>
```

> **Note:** This only works for when the element marked with `peer` is a
> previous siblings, that is, it comes before the element you want to start.

## Data Attribute

### LTR and RTL

To style an element based on the direction of the text, use the `_ltr` and
`_rtl` modifiers

```jsx {2}
<div dir="ltr">
  <Box _ltr={{ ml: "3" }} _rtl={{ mr: "3" }}>
    Hello
  </Box>
</div>
```

### State

To style an element based on its `data-{state}` attribute, use the corresponding
`_{state}` modifier

```jsx /_loading/
<Box data-loading _loading={{ bg: "gray.500" }}>
  Hello
</Box>
```

This works for common states like `data-active`, `data-disabled`, `data-focus`,
`data-hover`, `data-invalid`, `data-required`, and `data-valid`.

```jsx /_active/
<Box data-active _active={{ bg: "gray.500" }}>
  Hello
</Box>
```

### Orientation

To style an element based on its `data-orientation` attribute, use the
`_horizontal` and `_vertical` modifiers

```jsx
<Box
  data-orientation="horizontal"
  _horizontal={{ bg: "red.500" }}
  _vertical={{ bg: "blue.500" }}
>
  Hello
</Box>
```

## ARIA Attribute

To style an element based on its `aria-{state}=true` attribute, use the
corresponding `_{state}` prop

```jsx
<Box aria-expanded="true" _expanded={{ bg: "gray.500" }}>
  Hello
</Box>
```

## Reference

Here's a list of all the condition props you can use in Chakra:

<ConditionalStylesReferenceDoc />

## Customization

Chakra lets you create your own conditions, so you're not limited to the ones in
the default preset. Learn more about customizing conditions
[here](/docs/theming/customization/conditions).
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/css-variables.mdx

```text
---
title: CSS Variables
description: Using token-aware CSS variables in Chakra UI
---

## Overview

CSS variables have become the defacto way to create shared values on the web.
It's very useful to avoid prop interpolations, classname regeneration, and
reduce runtime evaluation of token values.

## Examples

### Basic

Use the `css` prop to create css variables

```jsx
<Box css={{ "--font-size": "18px" }}>
  <h3 style={{ fontSize: "calc(var(--font-size) * 2)" }}>Hello</h3>
  <p style={{ fontSize: "var(--font-size)" }}>Hello</p>
</Box>
```

### Access tokens

Use the full token path to access tokens

```jsx
<Box css={{ "--color": "colors.red.500" }}>
  <p style={{ color: "var(--color)" }}>Hello</p>
</Box>
```

Here's an example of how to access size tokens

```jsx
<Box css={{ "--size": "sizes.10" }}>
  <p style={{ width: "var(--size)", height: "var(--size)" }}>Hello</p>
</Box>
```

### Responsive Styles

Use the responsive syntax to make css variables responsive

```jsx
<Box css={{ "--font-size": { base: "18px", lg: "24px" } }}>
  <h3 style={{ fontSize: "calc(var(--font-size) * 2)" }}>Hello</h3>
  <p style={{ fontSize: "var(--font-size)" }}>Hello</p>
</Box>
```

### Color Opacity Modifier

When accessing color tokens, you can use the opacity modifier to access the
color with an opacity. The requirement is to use the `{}` syntax.

```jsx
<Box css={{ "--color": "{colors.red.500/40}" }}>
  <p style={{ color: "var(--color)" }}>Hello</p>
</Box>
```

### Virtual Color

Variables can point to a virtual color via the `colors.colorPalette.*` value.
This is useful for creating theme components.

```jsx
<Box colorPalette="blue" css={{ "--color": "colors.colorPalette.400" }}>
  <p style={{ color: "var(--color)" }}>Hello</p>
</Box>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/dark-mode.mdx

```text
---
title: Dark Mode
description: Learn how to use dark mode in Chakra UI applications
---

Chakra relies on the `next-themes` library to provide dark mode support. During
the installation process, the snippets required to get started are added to your
project via the CLI.

## Setup

If you haven't already, you can add the `next-themes` library to your project
via the CLI.

```bash
npx @chakra-ui/cli snippet add color-mode
```

The generated snippets consists of the following:

- `ColorModeProvider`: composes the `next-themes` provider component
- `useColorMode`: provides the current color mode and a function to toggle the
  color mode
- `useColorModeValue`: returns the correct value based on the current color mode
- `ColorModeButton`: can be used to toggle the color mode

## Usage

Wrap your app with the `ColorModeProvider` and use the `useColorMode` hook to
access and toggle the color mode.

```tsx
import { ColorModeProvider } from "@/components/ui/color-mode"
import { ChakraProvider, defaultSystem } from "@chakra-ui/react"

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <ChakraProvider theme={defaultSystem}>
      <ColorModeProvider>{children}</ColorModeProvider>
    </ChakraProvider>
  )
}
```

### Adding the dark mode toggle

Use the `ColorModeButton` component to toggle the color mode.

```tsx
import { ColorModeButton } from "@/components/ui/color-mode"

export default function Page({ children }: { children: React.ReactNode }) {
  return (
    <>
      <ColorModeButton />
      {children}
    </>
  )
}
```

### Styling dark mode

Use the `_dark` condition to style components for dark mode.

```tsx
<Box bg={{ base: "white", _dark: "black" }}>
  <Text>Hello</Text>
</Box>
```

or

```tsx
<Box bg="white" _dark={{ bg: "black" }}>
  <Text>Hello</Text>
</Box>
```

## Using semantic tokens

To reduce the amount of code you need to write, use semantic tokens to style
components for dark mode. This ensures the light and dark mode styles are
applied automatically and consistently.

Chakra provides a set of semantic tokens that you can use to style components
for dark mode. Learn more about
[semantic tokens](/docs/theming/semantic-tokens).

```tsx
<Box bg="bg.subtle">
  <Text>Hello</Text>
</Box>
```

## Forcing dark mode

### Element specific dark mode

To force dark mode, set the `dark` className on any parent element, or the root
element of your application.

```tsx /className="dark"/
<Box bg="black" className="dark">
  <Box bg="bg.subtle">
    <Text>Hello</Text>
  </Box>
</Box>
```

### Page specific dark mode

Use the `ColorModeProvider` component to set the dark mode for a page.

```tsx
<ColorModeProvider forcedTheme="dark">
  <Box bg="black" className="dark">
    <Box bg="bg.subtle">
      <Text>Hello</Text>
    </Box>
  </Box>
</ColorModeProvider>
```

> Follow this `next-themes` guide to learn more about
> [forcing color mode](https://github.com/pacocoursey/next-themes#force-page-to-a-theme).
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/focus-ring.mdx

```text
---
title: Focus Ring
description: How to style focus states in Chakra UI
---

The focus ring is used to identify the currently focused element on your page.
While this is important for accessibility, styling every component to have a
focus ring can be tedious.

Chakra UI provides the `focusRing` and `focusVisibleRing` style props to style
focus ring with ease. The value of the `focusRing` prop can be "outside",
"inside", or "mixed".

## Focus Ring

This focus ring maps to the `&:is(:focus, [data-focus])` CSS selector.

Here's how to style a button from scratch with a focus ring:

```tsx
<chakra.button px="4" py="2" focusRing="outside">
  Click me
</chakra.button>
```

## Focus Visible Ring

This focus ring maps to the `&:is(:focus-visible, [data-focus-visible])` CSS
selector.

```tsx
<chakra.button px="4" py="2" focusVisibleRing="outside">
  Click me
</chakra.button>
```

### Difference between Focus Ring and Focus Visible Ring

The Focus Visible Ring functions similarly to the Focus Ring, but with a key
difference: it only applies focus indicator styles when an element receives
keyboard focus.

This ensures that the focus ring is visible only when navigating via keyboard,
improving accessibility without affecting mouse interactions.

## Built-in Focus Ring

Here's a preview of the supported focus ring.

<ExampleTabs name="tokens/focus-ring" />

## Customization

### Ring Color

To change the focus ring color for a specific component, you can use the
`focusRingColor` prop.

```tsx
<Button focusRingColor="red.500">Click me</Button>
```

To change the color of the focus ring globally, you can configure the
`focusRing` semantic token.

```tsx {2-4}
const colors = defineSemanticTokens.colors({
  focusRing: {
    value: { base: "{colors.red.500}", _dark: "{colors.red.500}" },
  },
})
```

### Ring Width

To change the focus ring width for a specific component, you can use the
`focusRingWidth` prop.

```tsx
<Button focusRingWidth="2px">Click me</Button>
```

### Ring Style

To change the focus ring style for a specific component, you can use the
`focusRingStyle` prop.

```tsx
<Button focusRingStyle="dashed">Click me</Button>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/layer-styles.mdx

```text
---
title: Layer Styles
description: Learn how to use layer styles to define visual properties.
---

## Overview

Layer styles allows you to define visual properties. The common properties are:

- Color or text color
- Background color
- Border width and border color
- Box shadow
- Opacity

## Defining layer styles

Layer styles are defined using the `defineLayerStyles` function.

```js title="layer-styles.ts"
import { defineLayerStyles } from "@chakra-ui/react"

const layerStyles = defineLayerStyles({
  container: {
    description: "container styles",
    value: {
      bg: "gray.50",
      border: "2px solid",
      borderColor: "gray.500",
    },
  },
})
```

## Built-in layer styles

Chakra UI provides a set of built-in layer styles.

<ExamplePreview name="tokens/layer-style" />

## Updating the theme

To use the layer styles, update the `theme` object with the `layerStyles`
property.

```js title="theme.ts"
import { createSystem, defineConfig } from "@chakra-ui/react"
import { layerStyles } from "./layer-styles"

const config = defineConfig({
  theme: {
    layerStyles,
  },
})

export default createSystem(defaultConfig, config)
```

After updating the theme, run this command to generate the typings.

```bash
npx @chakra-ui/cli typegen
```

## Using layer styles

Now you can use `layerStyle` property in your components.

```jsx
<Box layerStyle="container">This is inside a container style</Box>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/overview.mdx

```text
---
title: Styling
description: All components are designed to be styled using props.
---

## Concepts

After installing Chakra UI, follow these guidelines to learn the key concepts:

- [Chakra Factory](/docs/styling/chakra-factory)
- [Responsive Design](/docs/styling/responsive-design)
- [CSS Variables](/docs/styling/css-variables)
- [Dark Mode](/docs/styling/dark-mode)
- [Color Opacity Modifier](/docs/styling/color-opacity-modifier)
- [Conditional Styles](/docs/styling/conditional-styles)
- [Virtual Color](/docs/styling/virtual-color)

## Compositions

After understanding the concepts, learn how to use these compositions to avoid
repeating styles:

- [Text Styles](/docs/styling/text-styles)
- [Layer Styles](/docs/styling/layer-styles)
- [Animation Styles](/docs/styling/animation-styles)
- [Focus Ring](/docs/styling/focus-ring)

## Style Props

Style props are the most fundamental way to style your components in Chakra UI.
They are basically css styles as props.
[Learn more about style props](/docs/styling/style-props/background)
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/responsive-design.mdx

```text
---
title: Responsive Design
description:
  Learn how to create responsive designs using Chakra UI's built-in responsive
  style props
---

## Overview

Responsive design is a fundamental aspect of modern web development, allowing
websites and applications to adapt seamlessly to different screen sizes and
devices.

:::info

Chakra uses a mobile-first breakpoint system and leverages min-width media
queries `@media(min-width)` when you write responsive styles.

:::

Chakra provides five breakpoints by default:

```ts
const breakpoints = {
  base: "0em", // 0px
  sm: "30em", // ~480px
  md: "48em", // ~768px
  lg: "62em", // ~992px
  xl: "80em", // ~1280px
  "2xl": "96em", // ~1536px
}
```

## Object syntax

Here's an example of how to change the font weight of a text on large screens

```jsx
<Text fontWeight="medium" lg={{ fontWeight: "bold" }}>
  Text
</Text>
```

or use the prop based modifier

```jsx
<Text fontWeight={{ base: "medium", lg: "bold" }}>Text</Text>
```

## Array syntax

Chakra also accepts arrays as values for responsive styles. Pass the
corresponding value for each breakpoint in the array. Using our previous code as
an example:

```jsx
<Text fontWeight={["medium", undefined, undefined, "bold"]}>Text</Text>
```

Notice the use of `undefined` for the breakpoints to skip the `md` and `lg`
breakpoints.

## Breakpoint targeting

### Breakpoint range

Chakra provides a way to target a range of breakpoints using the `To` notation.
To apply styles between the `md` and `xl` breakpoints, use the `mdToXl`
property:

```jsx
<Text fontWeight={{ mdToXl: "bold" }}>Text</Text>
```

> This text will only be bold from `md` to `xl` breakpoints.

### Only breakpoint

To target a single breakpoint, use the `Only` notation. Here's an example of how
to apply styles only in the `lg` breakpoint, using the `lgOnly` property:

```jsx
<Text fontWeight={{ lgOnly: "bold" }}>Text</Text>
```

## Hiding elements at breakpoint

Chakra provides the `hideFrom` and `hideBelow` utilities to hide elements at
specific breakpoints.

To hide an element from the `md` breakpoint, use the `hideFrom` utility:

```jsx
<Stack hideFrom="md">
  <Text>This text will be hidden from the `md` breakpoint</Text>
</Stack>
```

To hide an element below the `md` breakpoint, use the `hideBelow` utility:

```jsx
<Stack hideBelow="md">
  <Text>This text will be hidden below the `md` breakpoint</Text>
</Stack>
```

## Customizing Breakpoints

To learn how to customize breakpoints, please refer to the
[customizing breakpoints](/docs/theming/customization/breakpoints) section.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/background.mdx

```text
---
title: Background
description:
  JSX style props for styling background colors, gradients, and images.
---

## Background Attachment

Use `bgAttachment` to control the attachment of a background image.

```jsx
<Box bgAttachment="fixed" bgImage="url(...)" />
```

| Prop                                   | CSS Property            | Token Category |
| -------------------------------------- | ----------------------- | -------------- |
| `bgAttachment`, `backgroundAttachment` | `background-attachment` | -              |

## Background Blend Mode

Use `bgBlendMode` prop to control how an element's background image should blend
with the its background color.

```jsx
<Box bgBlendMode="multiply" bgColor="red.200" bgImage="url(...)" />
```

## Background Clip

Use `bgClip` to control the clipping of a background image.

```jsx
<Box bgClip="border-box" bgImage="url(...)" />
```

| Prop                       | CSS Property      | Token Category |
| -------------------------- | ----------------- | -------------- |
| `bgClip`, `backgroundClip` | `background-clip` | -              |

## Background Color

Use `bg`, `bgColor`, or `backgroundColor` props to set the background color of
an element.

```jsx
<Box bg="red.200" />
<Box bgColor="red.200" />

// with opacity modifier
<Box bg="blue.200/30" />
<Box bgColor="blue.200/30" />
```

| Prop                         | CSS Property       | Token Category |
| ---------------------------- | ------------------ | -------------- |
| `bg`, `background`           | `background`       | `colors`       |
| `bgColor`, `backgroundColor` | `background-color` | `colors`       |

## Background Origin

Use `bgOrigin` or `backgroundOrigin` to control the origin of a background
image.

```jsx
<Box bgOrigin="border-box" bgImage="url(...)" />
```

| Prop                           | CSS Property        | Token Category |
| ------------------------------ | ------------------- | -------------- |
| `bgOrigin`, `backgroundOrigin` | `background-origin` | -              |

## Background Position

Properties for controlling the src and position of a background image.

```jsx
<Box bgImage="url(...)" bgPosition="center" />
```

| Prop                                 | CSS Property       | Token Category |
| ------------------------------------ | ------------------ | -------------- |
| `bgPosition`, `backgroundPosition`   | `background-image` | -              |
| `bgPositionX`, `backgroundPositionX` | `background-image` | -              |
| `bgPositionY`, `backgroundPositionY` | `background-image` | -              |

## Background Repeat

Use `bgRepeat` or `backgroundRepeat` to control the repeat of a background
image.

```jsx
<Box bgRepeat="no-repeat" bgImage="url(...)" />
```

| Prop                           | CSS Property        | Token Category |
| ------------------------------ | ------------------- | -------------- |
| `bgRepeat`, `backgroundRepeat` | `background-repeat` | -              |

## Background Size

Use `bgSize` or `backgroundSize` to control the size of a background image.

```jsx
<Box bgSize="cover" bgImage="url(...)" />
```

| Prop                       | CSS Property      | Token Category |
| -------------------------- | ----------------- | -------------- |
| `bgSize`, `backgroundSize` | `background-size` | -              |

## Background Image

Use `bgImage` or `backgroundImage` to set the background image of an element.

```jsx
<Box bgImage="url(...)" />
<Box bgImage="radial-gradient(circle, #0000 45%, #000f 48%)" />
<Box bgImage="linear-gradient(black, white)" />

// with token reference
<Box bgImage="linear-gradient({colors.red.200}, {colors.blue.200})" />
```

| Prop                         | CSS Property       | Token Category |
| ---------------------------- | ------------------ | -------------- |
| `bgImage`, `backgroundImage` | `background-image` | assets         |

## Background Gradient

Properties to create a background gradient based on color stops.

```jsx
<Box bgGradient="to-r" gradientFrom="red.200" gradientTo="blue.200" />
```

| Prop           | CSS Property       | Token Category |
| -------------- | ------------------ | -------------- |
| `bgGradient`   | `background-image` | `gradients`    |
| `textGradient` | `background-image` | `gradients`    |
| `gradientFrom` | `--gradient-from`  | `colors`       |
| `gradientTo`   | `--gradient-to`    | `colors`       |
| `gradientVia`  | `--gradient-via`   | `colors`       |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/border.mdx

```text
---
title: Border
description: JSX style props for styling border and border radius.
---

## Border Radius

### All sides

Use the `rounded` or `borderRadius` props to apply border radius on all sides of
an element.

```jsx
<Box borderRadius="md" />
<Box rounded="md" /> // shorthand
```

| Prop                      | CSS Property    | Token Category |
| ------------------------- | --------------- | -------------- |
| `rounded`, `borderRadius` | `border-radius` | `radii`        |

### Specific sides

Use the `rounded{Left,Right,Top,Bottom}` or
`border{Left,Right,Top,Bottom}Radius` prop, to apply border radius on a specific
side of an element.

```jsx
<Box borderTopRadius="md" />
<Box roundedTop="md" /> // shorthand

<Box borderLeftRadius="md" />
<Box roundedLeft="md" /> // shorthand
```

Use the logical equivalent to make the border radius adapt based on the text
direction.

```jsx
<Box roundedStart="md" />
<Box roundedEnd="md" />
```

| Prop                                  | CSS Property                                           | Token Category |
| ------------------------------------- | ------------------------------------------------------ | -------------- |
| `roundedLeft`, `borderLeftRadius`     | `border-left-radius`                                   | `radii`        |
| `roundedRight`, `borderRightRadius`   | `border-right-radius`                                  | `radii`        |
| `roundedTop`, `borderTopRadius`       | `border-top-radius`                                    | `radii`        |
| `roundedBottom`, `borderBottomRadius` | `border-bottom-radius`                                 | `radii`        |
| `roundedStart`, `borderStartRadius`   | `border-start-start-radius`, `border-end-start-radius` | `radii`        |
| `roundedEnd`, `borderEndRadius`       | `border-start-end-radius`, `border-end-end-radius`     | `radii`        |

### Specific corners

Use the `border{Top,Bottom}{Left,Right}Radius` properties, or the shorthand
equivalent to round a specific corner.

```jsx
<Box borderTopLeftRadius="md" />
<Box roundedTopLeft="md" /> // shorthand
```

Use the logical properties to adapt based on the text direction.

```jsx
<Box borderStartStartRadius="md" />
<Box roundedStartStart="md" /> // shorthand
```

| Prop                                     | CSS Property                 | Token Category |
| ---------------------------------------- | ---------------------------- | -------------- |
| `roundedTopLeft`,`borderTopLeftRadius`   | `border-top-left-radius`     | `radii`        |
| `roundedTopRight`,`borderTopRight`       | `border-top-right-radius`    | `radii`        |
| `roundedBottomRight`,`borderBottomRight` | `border-bottom-right-radius` | `radii`        |
| `roundedBottomLeft`,`borderBottomLeft`   | `border-bottom-left-radius`  | `radii`        |

## Border Width

### All sides

Use the `borderWidth` prop to apply border width on all sides of an element.

> Chakra applies `borderStyle: solid` and a global border color by default.
> Specifying a border width is sufficient to apply the border.

```jsx
<Box borderWidth="1px" />
```

| Prop          | CSS Property   | Token Category |
| ------------- | -------------- | -------------- |
| `borderWidth` | `border-width` | `borderWidths` |

### Specific sides

Use the `border{Left|Right|Top|Bottom}Width` prop to apply border width on a
specific side of an element.

```jsx
<Box borderTopWidth="1px" />
<Box borderLeftWidth="1px" />
```

Use the logical equivalent to make the border width adapt based on the text
direction.

```jsx
<Box borderInlineStartWidth="1px" />
<Box borderInlineWidth="1px" /> // shorthand
```

| Prop                                          | CSS Property                | Token Category |
| --------------------------------------------- | --------------------------- | -------------- |
| `borderTopWidth`                              | `border-top-width`          | `borderWidths` |
| `borderLeftWidth`                             | `border-left-width`         | `borderWidths` |
| `borderRightWidth`                            | `border-right-width`        | `borderWidths` |
| `borderBottomWidth`                           | `border-bottom-width`       | `borderWidths` |
| `borderStartWidth` , `borderInlineStartWidth` | `border-{start+end}-width`  |
| `borderEndWidth` , `borderInlineEndWidth`     | `border-{start+end}-width`  |
| `borderXWidth` , `borderInlineWidth`          | `border-{left,right}-width` | `borderWidths` |
| `borderYWidth` , `borderBlockWidth`           | `border-{top,bottom}-width` | `borderWidths` |

## Border Color

### All sides

Use the `borderColor` prop to apply border color on all sides of an element.

```jsx
<Box borderColor="red.400" />

// with opacity modifier
<Box borderColor="red.400/20" />
```

### Specific sides

Use the `border{Left,Right,Top,Bottom}Color` prop to apply border color on a
specific side of an element.

```jsx
<Box borderTopColor="red.400" />
<Box borderLeftColor="red.400" />
```

Use the logical properties to adapt based on the text direction.

```jsx
<Box borderStartColor="red.400" />
<Box borderEndColor="red.400" />
```

| Prop                                          | CSS Property               | Token Category |
| --------------------------------------------- | -------------------------- | -------------- |
| `borderColor`                                 | `border-color`             | `colors`       |
| `borderTopColor`                              | `border-top-color`         | `colors`       |
| `borderLeftColor`                             | `border-left-color`        | `colors`       |
| `borderRightColor`                            | `border-right-color`       | `colors`       |
| `borderBottomColor`                           | `border-bottom-color`      | `colors`       |
| `borderStartColor` , `borderInlineStartColor` | `border-{start,end}-color` | `colors`       |
| `borderEndColor` , `borderInlineEndColor`     | `border-{start,end}-color` | `colors`       |
| `borderXColor`, `borderInlineColor`           | `border-inline-color`      | `colors`       |
| `borderYColor`, `borderBlockColor`            | `border-block-color`       | `colors`       |

## Divide Width

Use the `divide{X,Y}Width` prop to apply border width between elements. It uses
the CSS selector `> * + *` to apply the `border*` properties.

```jsx
<Box divideXWidth="1px">
  <Box>1</Box>
  <Box>2</Box>
</Box>

<Box divideYWidth="1px">
  <Box>1</Box>
  <Box>2</Box>
</Box>
```

| Prop          | CSS Property                        | Token Category |
| ------------- | ----------------------------------- | -------------- |
| `divideWidth` | `border-{inline,block}-start-width` | `borderWidths` |

## Divide Color

Use the `divideColor` prop to apply border color between elements.

```jsx
<Box divideColor="red.400">
  <Box>1</Box>
  <Box>2</Box>
</Box>
```

| Prop          | CSS Property     | Token Category |
| ------------- | ---------------- | -------------- |
| `divideColor` | `--divide-color` | `colors`       |

## Divide Style

Use the `divideStyle` prop to apply border style between elements.

```jsx
<Box divideX="2px" divideStyle="dashed">
  <Box>1</Box>
  <Box>2</Box>
</Box>
```

| Prop          | CSS Property     | Token Category |
| ------------- | ---------------- | -------------- |
| `divideStyle` | `--divide-style` | `borderStyle`  |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/display.mdx

```text
---
title: Display
description: Style props for styling display of an element.
---

## Display Property

```jsx
<Box display="flex" />

// responsive
<Box display={{ base: "none", md: "block" }} />
```

| Prop      | CSS Property | Token Category |
| --------- | ------------ | -------------- |
| `display` | `display`    | -              |

## Hiding Elements

### Hide From

Use the `hideFrom` prop to hide an element from a specific breakpoint.

```jsx
<Box display="flex" hideFrom="md" />
```

| Prop       | CSS Property | Token Category |
| ---------- | ------------ | -------------- |
| `hideFrom` | `display`    | `breakpoints`  |

### Hide Below

Use the `hideBelow` prop to hide an element below a specific breakpoint.

```jsx
<Box display="flex" hideBelow="md" />
```

| Prop        | CSS Property | Token Category |
| ----------- | ------------ | -------------- |
| `hideBelow` | `display`    | `breakpoints`  |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/divide.mdx

```text
---
title: Divide
description: JSX style props to add dividers between elements
---

## Divide X

Use the `divideX` prop to add a divider between elements horizontally.

```jsx
<Box divideX="2px">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
</Box>
```

| Prop      | CSS Property                | Token Category |
| --------- | --------------------------- | -------------- |
| `divideX` | `border-inline-start-width` | -              |

## Divide Y

Use the `divideY` prop to add a divider between elements vertically.

```jsx
<Box divideY="2px">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
</Box>
```

| Prop      | CSS Property       | Token Category |
| --------- | ------------------ | -------------- |
| `divideY` | `border-top-width` | -              |

## Divide Color

Use the `divideColor` prop to add a divider color.

```jsx
<Box divideY="2px" divideColor="red.200">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
</Box>
```

| Prop          | CSS Property       | Token Category |
| ------------- | ------------------ | -------------- |
| `divideColor` | `border-top-color` | -              |

## Divide Style

Use the `divideStyle` prop to add a divider style.

```jsx
<Box divideY="2px" divideStyle="dashed">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
</Box>
```

| Prop          | CSS Property   | Token Category |
| ------------- | -------------- | -------------- |
| `divideStyle` | `border-style` | -              |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/effects.mdx

```text
---
title: Effects
description: JSX style props for styling box shadows, opacity, and more
---

## Box Shadow

Use the `shadow` or `boxShadow` prop to apply a box shadow to an element.

```jsx
// hardcoded values
<Box shadow="12px 12px 2px 1px rgba(0, 0, 255, .2)" />

// token values
<Box shadow="lg" />
```

| Prop                   | CSS Property     | Token Category |
| ---------------------- | ---------------- | -------------- |
| `shadows`, `boxShadow` | `box-shadow`     | `shadows`      |
| `shadowColor`          | `--shadow-color` | `colors`       |

## Box Shadow Color

Use the `shadowColor` prop to set the color of a box shadow. This prop maps to
the `--shadow-color` CSS variable.

```jsx
<Box shadow="60px -16px var(--shadow-color)" shadowColor="red" />
```

| Prop          | CSS Property     | Token Category |
| ------------- | ---------------- | -------------- |
| `shadowColor` | `--shadow-color` | `colors`       |

## Opacity

Use the `opacity` prop to set the opacity of an element.

```jsx
<Box opacity="0.5" />
```

| Prop      | CSS Property | Token Category |
| --------- | ------------ | -------------- |
| `opacity` | `opacity`    | `opacity`      |

## Mix Blend Mode

Use the `mixBlendMode` prop to control how an element's content should be
blended with the background.

```jsx
<Box bg="red.400">
  <Image src="..." mixBlendMode="hard-light" />
</Box>
```

| Prop           | CSS Property     | Token Category |
| -------------- | ---------------- | -------------- |
| `mixBlendMode` | `mix-blend-mode` | -              |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/filters.mdx

```text
---
title: Filters
description: JSX style props for applying various filters to elements.
---

## Filter

Use the `filter` prop to apply visual effects like blur or color shift to an
element.

```jsx
<Box filter="blur(5px)" />
<Box filter="grayscale(80%)" />
```

| Prop     | CSS Property | Token Category |
| -------- | ------------ | -------------- |
| `filter` | `filter`     | -              |

## Blur

Use the `blur` prop to apply a blur effect to an element. The requirement for
this prop is to set the `filter` prop to `auto`.

```jsx
// hardcoded value
<Box filter="auto" blur="5px" />

// token value
<Box filter="auto" blur="sm" />
```

| Prop   | CSS Property | Token Category |
| ------ | ------------ | -------------- |
| `blur` | `--blur`     | `blurs`        |

## Contrast

Use the `contrast` prop to apply a contrast effect to an element. The
requirement for this prop is to use the `filter` prop and set it to `auto`.

```jsx
<Box filter="auto" contrast="0.3" />
```

| Prop       | CSS Property | Token Category |
| ---------- | ------------ | -------------- |
| `contrast` | `--contrast` | -              |

## Drop Shadow

Use the `dropShadow` prop to apply a drop shadow effect to an element. The
requirement for this prop is to use the `filter` prop and set it to `auto`.

```jsx
<Box filter="auto" dropShadow="0px 0px 10px rgba(0, 0, 0, 0.5)" />
```

| Prop         | CSS Property    | Token Category |
| ------------ | --------------- | -------------- |
| `dropShadow` | `--drop-shadow` | -              |

## Grayscale

Use the `grayscale` prop to apply a grayscale effect to an element. The
requirement for this prop is to use the `filter` prop and set it to `auto`.

```jsx
<Box filter="auto" grayscale="64%" />
```

| Prop        | CSS Property  | Token Category |
| ----------- | ------------- | -------------- |
| `grayscale` | `--grayscale` | -              |

## Hue Rotate

Use the `hueRotate` prop to apply a hue rotate effect to an element. The
requirement for this prop is to use the `filter` prop and set it to `auto`.

```jsx
<Box filter="auto" hueRotate="30deg" />
```

| Prop        | CSS Property   | Token Category |
| ----------- | -------------- | -------------- |
| `hueRotate` | `--hue-rotate` | -              |

## Invert

Use the `invert` prop to apply an invert effect to an element. The requirement
for this prop is to use the `filter` prop and set it to `auto`.

```jsx
<Box filter="auto" invert="40%" />
```

| Prop     | CSS Property | Token Category |
| -------- | ------------ | -------------- |
| `invert` | `--invert`   | -              |

## Saturate

Use the `saturate` prop to apply a saturate effect to an element. The
requirement for this prop is to use the `filter` prop and set it to `auto`.

```jsx
<Box filter="auto" saturate="0.4" />
```

| Prop       | CSS Property | Token Category |
| ---------- | ------------ | -------------- |
| `saturate` | `--saturate` | -              |

## Sepia

Use the `sepia` prop to apply a sepia effect to an element. The requirement for
this prop is to use the `filter` prop and set it to `auto`.

```jsx
<Box filter="auto" sepia="0.4" />
```

| Prop    | CSS Property | Token Category |
| ------- | ------------ | -------------- |
| `sepia` | `--sepia`    | -              |

## Backdrop Filter

Use the `backdropFilter` prop to apply visual effects like blur or color shift
to the area behind an element. This creates a translucent effect.

```jsx
<Box backdropFilter="blur(5px)" />
<Box backdropFilter="grayscale(80%)" />
```

| Prop             | CSS Property      | Token Category |
| ---------------- | ----------------- | -------------- |
| `backdropFilter` | `backdrop-filter` | -              |

## Backdrop Blur

Use the `backdropBlur` prop to apply a blur effect to the area behind an
element. The requirement for this prop is to set the `backdropFilter` prop to
`auto`.

```jsx
// hardcoded value
<Box backdropFilter="auto" backdropBlur="5px" />

// token value
<Box backdropFilter="auto" backdropBlur="sm" />
```

| Prop           | CSS Property      | Token Category |
| -------------- | ----------------- | -------------- |
| `backdropBlur` | `--backdrop-blur` | `blurs`        |

## Backdrop Contrast

Use the `backdropContrast` prop to apply a contrast effect to the area behind an
element. The requirement for this prop is to use the `backdropFilter` prop and
set it to `auto`.

```jsx
<Box backdropFilter="auto" backdropContrast="0.3" />
```

| Prop               | CSS Property          | Token Category |
| ------------------ | --------------------- | -------------- |
| `backdropContrast` | `--backdrop-contrast` | -              |

## Backdrop Grayscale

Use the `backdropGrayscale` prop to apply a grayscale effect to the area behind
an element. The requirement for this prop is to use the `backdropFilter` prop
and set it to `auto`.

```jsx
<Box backdropFilter="auto" backdropGrayscale="64%" />
```

| Prop                | CSS Property           | Token Category |
| ------------------- | ---------------------- | -------------- |
| `backdropGrayscale` | `--backdrop-grayscale` | -              |

## Backdrop Hue Rotate

Use the `backdropHueRotate` prop to apply a hue rotate effect to the area behind
an element. The requirement for this prop is to use the `backdropFilter` prop
and set it to `auto`.

```jsx
<Box backdropFilter="auto" backdropHueRotate="30deg" />
```

| Prop                | CSS Property            | Token Category |
| ------------------- | ----------------------- | -------------- |
| `backdropHueRotate` | `--backdrop-hue-rotate` | -              |

## Backdrop Invert

Use the `backdropInvert` prop to apply an invert effect to the area behind an
element. The requirement for this prop is to use the `backdropFilter` prop and
set it to `auto`.

```jsx
<Box backdropFilter="auto" backdropInvert="40%" />
```

| Prop             | CSS Property        | Token Category |
| ---------------- | ------------------- | -------------- |
| `backdropInvert` | `--backdrop-invert` | -              |

## Backdrop Opacity

Use the `backdropOpacity` prop to apply an opacity effect to the area behind an
element. The requirement for this prop is to use the `backdropFilter` prop and
set it to `auto`.

```jsx
<Box backdropFilter="auto" backdropOpacity="0.4" />
```

| Prop              | CSS Property         | Token Category |
| ----------------- | -------------------- | -------------- |
| `backdropOpacity` | `--backdrop-opacity` | -              |

## Backdrop Saturate

Use the `backdropSaturate` prop to apply a saturate effect to the area behind an
element. The requirement for this prop is to use the `backdropFilter` prop and
set it to `auto`.

```jsx
<Box backdropFilter="auto" backdropSaturate="0.4" />
```

| Prop               | CSS Property          | Token Category |
| ------------------ | --------------------- | -------------- |
| `backdropSaturate` | `--backdrop-saturate` | -              |

## Backdrop Sepia

Use the `backdropSepia` prop to apply a sepia effect to the area behind an
element. The requirement for this prop is to use the `backdropFilter` prop and
set it to `auto`.

```jsx
<Box backdropFilter="auto" backdropSepia="0.4" />
```

| Prop            | CSS Property       | Token Category |
| --------------- | ------------------ | -------------- |
| `backdropSepia` | `--backdrop-sepia` | -              |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/flex-and-grid.mdx

```text
---
title: Flex and Grid
description: JSX style props to control flex and grid layouts
---

## Flex Basis

Use the `flexBasis` prop to set the initial main size of a flex item.

```jsx
<Flex>
  <Box flexBasis="25%" />
  <Box flexBasis="25%" />
  <Box flexBasis="50%" />
</Flex>
```

| Prop        | CSS Property | Token Category |
| ----------- | ------------ | -------------- |
| `flexBasis` | `flex-basis` | -              |

## Flex Direction

Use the `flexDir` or `flexDirection` prop to set the direction of the main axis
in a flex container.

```jsx
<Box display="flex" flexDirection="column">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

When using `Flex` component, the `direction` prop is aliased to `flexDirection`.

```jsx
<Flex direction="column">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Flex>
```

| Prop                       | CSS Property     | Token Category |
| -------------------------- | ---------------- | -------------- |
| `flexDir`, `flexDirection` | `flex-direction` | -              |

## Flex Wrap

Use the `flexWrap` prop to set whether flex items are forced onto one line or
can wrap onto multiple lines.

```jsx
<Box display="flex" flexWrap="wrap">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

When using `Flex` component, the `wrap` prop is aliased to `flexWrap`.

```jsx
<Flex wrap="wrap">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Flex>
```

| Prop       | CSS Property | Token Category |
| ---------- | ------------ | -------------- |
| `flexWrap` | `flex-wrap`  | -              |

## Flex

Use the `flex` prop to define the flexibility of a flex container or item.

```jsx
<Flex>
  <Box flex="1" />
  <Box />
</Flex>
```

| Prop   | CSS Property | Token Category |
| ------ | ------------ | -------------- |
| `flex` | `flex`       | -              |

## Flex Grow

Use the `flexGrow` prop to set the flex grow factor of a flex item.

```jsx
<Flex>
  <Box flexGrow="0" />
  <Box flexGrow="1" />
</Flex>
```

| Prop       | CSS Property | Token Category |
| ---------- | ------------ | -------------- |
| `flexGrow` | `flex-grow`  | -              |

## Flex Shrink

Use the `flexShrink` prop to set the flex shrink factor of a flex item.

```jsx
<Flex>
  <Box flexShrink="0" />
  <Box flexShrink="1" />
</Flex>
```

| Prop         | CSS Property  | Token Category |
| ------------ | ------------- | -------------- |
| `flexShrink` | `flex-shrink` | -              |

## Order

Use the `order` prop to set the order of a flex item.

```jsx
<Flex>
  <Box order="0" />
  <Box order="1" />
</Flex>
```

| Prop    | CSS Property | Token Category |
| ------- | ------------ | -------------- |
| `order` | `order`      | -              |

## Gap

Use the `gap` prop to set the gap between items in a flex or grid container.

```jsx
<Flex gap="4">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Flex>
```

| Prop  | CSS Property | Token Category |
| ----- | ------------ | -------------- |
| `gap` | `gap`        | `spacing`      |

## Grid Template Columns

Use the `gridTemplateColumns` prop to define the columns of a grid container.

```jsx
<Box display="grid" gridTemplateColumns="repeat(3, minmax(0, 1fr))">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

When using `Grid` component, the `templateColumns` prop is aliased to
`gridTemplateColumns`.

```jsx
<Grid templateColumns="repeat(3, minmax(0, 1fr))">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Grid>
```

## Grid Template Start/End

Use the `gridTemplateStart` and `gridTemplateEnd` props to define the start and
end of a grid container.

```jsx
<Box display="grid" gridTemplateColumns="repeat(3, minmax(0, 1fr))">
  <Box>Item 1</Box>
  <Box gridColumn="span 2 / span 2">Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

| Prop                | CSS Property          | Token Category |
| ------------------- | --------------------- | -------------- |
| `gridTemplateStart` | `grid-template-start` | -              |
| `gridTemplateEnd`   | `grid-template-end`   | -              |

## Grid Template Rows

Use the `gridTemplateRows` prop to define the rows of a grid container.

```jsx
<Box display="grid" gap="4" gridTemplateRows="repeat(3, minmax(0, 1fr))">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

| Prop               | CSS Property         | Token Category |
| ------------------ | -------------------- | -------------- |
| `gridTemplateRows` | `grid-template-rows` | -              |

## Grid Row Start/End

Use the `gridRowStart` and `gridRowEnd` props to define the start and end of a
grid item.

```jsx
<Box display="grid" gap="4" gridTemplateRows="repeat(3, minmax(0, 1fr))">
  <Box gridRowStart="1" gridRowEnd="3">
    Item 1
  </Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

| Prop           | CSS Property     | Token Category |
| -------------- | ---------------- | -------------- |
| `gridRowStart` | `grid-row-start` | -              |
| `gridRowEnd`   | `grid-row-end`   | -              |

## Grid Autoflow

Use the `gridAutoFlow` prop to define how auto-placed items get flowed into the
grid.

```jsx
<Box display="grid" gridAutoFlow="row">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

| Prop           | CSS Property     | Token Category |
| -------------- | ---------------- | -------------- |
| `gridAutoFlow` | `grid-auto-flow` | -              |

## Grid Auto Columns

Use the `gridAutoColumns` prop to specify the size of the grid columns that were
created without an explicit size.

```jsx
<Box display="grid" gridAutoColumns="120px">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

| Prop              | CSS Property        | Token Category |
| ----------------- | ------------------- | -------------- |
| `gridAutoColumns` | `grid-auto-columns` | -              |

## Grid Auto Rows

Use the `gridAutoRows` prop to specify the size of the grid rows that were
created without an explicit size.

```jsx
<Box display="grid" gridTemplateRows="200px" gridAutoRows="120px">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

| Prop           | CSS Property     | Token Category |
| -------------- | ---------------- | -------------- |
| `gridAutoRows` | `grid-auto-rows` | -              |

## Justify Content

Use the `justifyContent` prop to align items along the main axis of a flex
container.

```jsx
<Box display="flex" justifyContent="center">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

When using the `Flex` component, the `justify` prop is aliased to
`justifyContent`.

```jsx
<Flex justify="center">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Flex>
```

| Prop             | CSS Property      | Token Category |
| ---------------- | ----------------- | -------------- |
| `justifyContent` | `justify-content` | -              |

## Justify Items

Use the `justifyItems` prop to control the alignment of grid items within their
scope.

```jsx
<Box display="grid" justifyItems="center">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

| Prop           | CSS Property    | Token Category |
| -------------- | --------------- | -------------- |
| `justifyItems` | `justify-items` | -              |

## Align Content

Use the `alignContent` prop to align rows of content along the cross axis of a
flex container when there's extra space.

```jsx
<Box display="flex" alignContent="center">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

When using the `Flex` component, the `align` prop is aliased to `alignContent`.

```jsx
<Flex align="center">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Flex>
```

| Prop           | CSS Property    | Token Category |
| -------------- | --------------- | -------------- |
| `alignContent` | `align-content` | -              |

## Align Items

Use the `alignItems` prop to control the alignment of grid items within their
scope.

```jsx
<Box display="grid" alignItems="center">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

## Align Self

Use the `alignSelf` prop to control the alignment of a grid item within its
scope.

```jsx
<Box display="grid">
  <Box alignSelf="center">Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

| Prop        | CSS Property | Token Category |
| ----------- | ------------ | -------------- |
| `alignSelf` | `align-self` | -              |

## Place Content

Use the `placeContent` prop to align content along both the block and inline
directions at once. It works like `justifyContent` and `alignContent` combined,
and can be used in flex and grid containers.

```jsx
<Flex placeContent="center">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Flex>
```

| Prop           | CSS Property    | Token Category |
| -------------- | --------------- | -------------- |
| `placeContent` | `place-content` | -              |

## Place Items

Use the `placeItems` prop to align items along both the block and inline
directions at once. It works like `justifyItems` and `alignItems` combined, and
can be used in flex and grid containers.

```jsx
<Box display="grid" placeItems="center">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

| Prop         | CSS Property  | Token Category |
| ------------ | ------------- | -------------- |
| `placeItems` | `place-items` | -              |

## Place Self

Use the `placeSelf` prop to align a grid item along both the block and inline
directions at once.

```jsx
<Box display="grid">
  <Box placeSelf="center">Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

| Prop        | CSS Property | Token Category |
| ----------- | ------------ | -------------- |
| `placeSelf` | `place-self` | -              |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/interactivity.mdx

```text
---
title: Interactivity
description: JSX style props to enhance interactivity on an element
---

## Accent Color

Use the `accentColor` prop to set the accent color for browser generated
user-interface controls.

```jsx
// hardcoded
<label>
  <chakra.input type="checkbox" accentColor="#3b82f6" />
</label>

// token value
<label>
  <chakra.input type="checkbox" accentColor="blue.500" />
</label>
```

| Prop          | CSS Property   | Token Category |
| ------------- | -------------- | -------------- |
| `accentColor` | `accent-color` | `colors`       |

## Appearance

Use the `appearance` prop to set the appearance of an element.

```jsx
<chakra.select appearance="none">
  <option value="1">Option 1</option>
  <option value="2">Option 2</option>
  <option value="3">Option 3</option>
</chakra.select>
```

| Prop         | CSS Property | Token Category |
| ------------ | ------------ | -------------- |
| `appearance` | `appearance` | -              |

## Caret Color

Use the `caretColor` prop to set the color of the text cursor (caret) in an
input or textarea

```jsx
// hardcoded
<chakra.input caretColor="#3b82f6" />

// token value
<chakra.input caretColor="blue.500" />
```

| Prop         | CSS Property  | Token Category |
| ------------ | ------------- | -------------- |
| `caretColor` | `caret-color` | `colors`       |

## Cursor

Use the `cursor` prop to set the mouse cursor image to show when the mouse
pointer is over an element.

```jsx
<Box cursor="pointer" />
```

| Prop     | CSS Property | Token Category |
| -------- | ------------ | -------------- |
| `cursor` | `cursor`     | -              |

## Pointer Events

Use the `pointerEvents` prop to control how pointer events are handled on an
element.

```jsx
<Box pointerEvents="none">Can't click me</Box>
```

| Prop            | CSS Property     | Token Category |
| --------------- | ---------------- | -------------- |
| `pointerEvents` | `pointer-events` | -              |

## Resize

Use the `resize` prop to control whether an element is resizable, and if so, in
which directions.

```jsx
<chakra.textarea resize="both" />
<chakra.textarea resize="horizontal" />
<chakra.textarea resize="vertical" />
```

| Prop     | CSS Property | Token Category |
| -------- | ------------ | -------------- |
| `resize` | `resize`     | -              |

## Scrollbar

Use the `scrollbar` prop to customize the appearance of scrollbars.

```jsx
<Box scrollbar="hidden" maxH="100px" overflowY="auto">
  Scrollbar hidden
</Box>
```

## Scroll Behavior

Use the `scrollBehavior` prop to set the behavior for a scrolling box when
scrolling is triggered by the navigation or JavaScript code.

```jsx
<Box maxH="100px" overflowY="auto" scrollBehavior="smooth">
  <div>Scroll me</div>
</Box>
```

| Prop             | CSS Property      | Token Category |
| ---------------- | ----------------- | -------------- |
| `scrollBehavior` | `scroll-behavior` | -              |

## Scroll Margin

Use the `scrollMargin*` prop to set margins around scroll containers.

```jsx
<Box maxH="100px" overflowY="auto" scrollMarginY="2">
  Scrollbar Container with block padding
</Box>
```

| Prop                                  | CSS Property                 | Token Category |
| ------------------------------------- | ---------------------------- | -------------- |
| `scrollMarginX` ,`scrollMarginInline` | `scroll-margin-inline`       | `spacing`      |
| `scrollMarginInlineStart`             | `scroll-margin-inline-start` | `spacing`      |
| `scrollMarginInlineEnd`               | `scroll-margin-inline-end`   | `spacing`      |
| `scrollMarginY` , `scrollMarginBlock` | `scroll-margin-block`        | `spacing`      |
| `scrollMarginBlockStart`              | `scroll-margin-block-start`  | `spacing`      |
| `scrollMarginBlockEnd`                | `scroll-margin-block-end`    | `spacing`      |
| `scrollMarginLeft`                    | `scroll-margin-left`         | `spacing`      |
| `scrollMarginRight`                   | `scroll-margin-right`        | `spacing`      |
| `scrollMarginTop`                     | `scroll-margin-top`          | `spacing`      |
| `scrollMarginBottom`                  | `scroll-margin-bottom`       | `spacing`      |

## Scroll Padding

Use the `scrollPadding*` prop to set padding inside scroll containers.

```jsx
<Box maxH="100px" overflowY="auto" scrollPaddingY="2">
  Scrollbar Container with block padding
</Box>
```

| Prop                                     | CSS Property                  | Token Category |
| ---------------------------------------- | ----------------------------- | -------------- |
| `scrollPaddingX` , `scrollPaddingInline` | `scroll-padding-inline`       | `spacing`      |
| `scrollPaddingInlineStart`               | `scroll-padding-inline-start` | `spacing`      |
| `scrollPaddingInlineEnd`                 | `scroll-padding-inline-end`   | `spacing`      |
| `scrollPaddingY` , `scrollPaddingBlock`  | `scroll-padding-block`        | `spacing`      |
| `scrollPaddingBlockStart`                | `scroll-padding-block-start`  | `spacing`      |
| `scrollPaddingBlockEnd`                  | `scroll-padding-block-end`    | `spacing`      |
| `scrollPaddingLeft`                      | `scroll-padding-left`         | `spacing`      |
| `scrollPaddingRight`                     | `scroll-padding-right`        | `spacing`      |
| `scrollPaddingTop`                       | `scroll-padding-top`          | `spacing`      |
| `scrollPaddingBottom`                    | `scroll-padding-bottom`       | `spacing`      |

## Scroll Snap Margin

Use the `scrollSnapMargin*` prop to set margins around scroll containers.

```jsx
<Box maxH="100px" overflowY="auto" scrollSnapMarginY="2">
  Scrollbar Container with block padding
</Box>
```

| Prop                     | CSS Property           | Token Category |
| ------------------------ | ---------------------- | -------------- |
| `scrollSnapMargin`       | `scroll-margin`        | `spacing`      |
| `scrollSnapMarginTop`    | `scroll-margin-top`    | `spacing`      |
| `scrollSnapMarginBottom` | `scroll-margin-bottom` | `spacing`      |
| `scrollSnapMarginLeft`   | `scroll-margin-left`   | `spacing`      |
| `scrollSnapMarginRight`  | `scroll-margin-right`  | `spacing`      |

## Scroll Snap Type

Use the `scrollSnapType` prop to control how strictly snap points are enforced
in a scroll container.

```jsx
<Box maxH="100px" overflowY="auto" scrollSnapType="x">
  Scroll container with x snap type
</Box>
```

| Value  |                                      |
| ------ | ------------------------------------ |
| `none` | `none`                               |
| `x`    | `x var(--scroll-snap-strictness)`    |
| `y`    | `y var(--scroll-snap-strictness)`    |
| `both` | `both var(--scroll-snap-strictness)` |

## Scroll Snap Strictness

Use the `scrollSnapStrictness` prop to set the scroll snap strictness of an
element. This requires `scrollSnapType` to be set to `x`,`y` or `both`.

It's values can be `mandatory` or `proximity` values, and maps to
`var(--scroll-snap-strictness)`.

```jsx
<Box maxH="100px" overflowY="auto" scrollSnapStrictness="proximity">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
</Box>
```

| Prop                   | CSS Property               | Token Category |
| ---------------------- | -------------------------- | -------------- |
| `scrollSnapStrictness` | `--scroll-snap-strictness` | -              |

## Touch Action

Use the `touchAction` prop to control how an element's region can be manipulated
by a touchscreen user

```jsx
<Box touchAction="none" />
```

| Prop          | CSS Property   | Token Category |
| ------------- | -------------- | -------------- |
| `touchAction` | `touch-action` | -              |

## User Select

Use the `userSelect` prop to control whether the user can select text within an
element.

```jsx
<Box userSelect="none">
  <p>Can't Select me</p>
</Box>
```

| Prop         | CSS Property  | Token Category |
| ------------ | ------------- | -------------- |
| `userSelect` | `user-select` | -              |

## Will Change

Use the `willChange` prop to hint browsers how an element's property is expected
to change.

```jsx
<Box willChange="transform" />
```

| Prop         | CSS Property  | Token Category |
| ------------ | ------------- | -------------- |
| `willChange` | `will-change` | -              |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/layout.mdx

```text
---
title: Layout
description:
  JSX style props to control the width, height, and spacing of elements
---

## Aspect Ratio

Use the `aspectRatio` prop to set the desired aspect ratio of an element.

```jsx
// raw value
<Box aspectRatio="1.2" />

// token
<Box aspectRatio="square" />
```

| Prop          | CSS Property   | Token Category |
| ------------- | -------------- | -------------- |
| `aspectRatio` | `aspect-ratio` | `aspectRatios` |

## Break

### Break After

Use the `breakAfter` prop to set how page, column, or region breaks should
behave after an element.

```jsx
<Box columns="2">
  <Box>Item 1</Box>
  <Box breakAfter="page">Item 2</Box>
</Box>
```

| Prop         | CSS Property  | Token Category |
| ------------ | ------------- | -------------- |
| `breakAfter` | `break-after` | -              |

### Break Before

Use the `breakBefore` prop to set how page, column, or region breaks should
behave before an element.

```jsx
<Box columns="2">
  <Box>Item 1</Box>
  <Box breakBefore="page">Item 2</Box>
</Box>
```

| Prop          | CSS Property   | Token Category |
| ------------- | -------------- | -------------- |
| `breakBefore` | `break-before` | -              |

### Break Inside

Use the `breakInside` prop to set how page, column, or region breaks should
behave inside an element.

```jsx
<Box columns="2">
  <Box>Item 1</Box>
  <Box breakInside="avoid">Item 2</Box>
</Box>
```

| Prop          | CSS Property   | Token Category |
| ------------- | -------------- | -------------- |
| `breakInside` | `break-inside` | -              |

## Box Decoration Break

Use the `boxDecorationBreak` prop to set how box decorations should behave when
the box breaks across multiple lines, columns, or pages.

```jsx /boxDecorationBreak/
<Box bgImage="linear-gradient(red, blue)" boxDecorationBreak="clone">
  Chakra is <br /> great!
</Box>
```

| Prop                 | CSS Property           | Token Category |
| -------------------- | ---------------------- | -------------- |
| `boxDecorationBreak` | `box-decoration-break` | -              |

## Box Sizing

Use the `boxSizing` prop to set the box sizing of an element.

```jsx /boxSizing/
<Box boxSizing="border-box" padding="4" width="8" height="8" />
```

| Prop        | CSS Property | Token Category |
| ----------- | ------------ | -------------- |
| `boxSizing` | `box-sizing` | -              |

## Columns

Use the `columns` prop to set the number of columns for an element.

```jsx
<Box columns={2} />
```

| Prop      | CSS Property | Token Category |
| --------- | ------------ | -------------- |
| `columns` | `columns`    | -              |

## Float

Use the `float` prop to set the float of an element.

```jsx /float/
<Box>
  <Text>As much mud in the streets...</Text>
  <Box float="left">Float me</Box>
</Box>
```

| Prop    | CSS Property | Token Category |
| ------- | ------------ | -------------- |
| `float` | `float`      | -              |

## Clear

Use the `clear` prop to set whether an element must be moved below (cleared)
floating elements that precede it.

```jsx
<Box>
  <Box float="left">Left</Box>
  <Box float="right">Right</Box>
  <Box clear="none">
    As much mud in the streets as if the waters had but newly retired from the
    face of the earth, and it would not be wonderful to meet a Megalosaurus,
    forty feet long or so, waddling like an elephantine lizard up Holborn Hill.
  </Box>
</Box>
```

| Prop    | CSS Property | Token Category |
| ------- | ------------ | -------------- |
| `clear` | `clear`      | -              |

## Isolation

Use the `isolation` prop to set whether an element should explicitly create a
new stacking context.

```jsx
<Box isolation="isolate">
  <Box bg="red.500" width="10" height="10" />
</Box>
```

| Prop        | CSS Property | Token Category |
| ----------- | ------------ | -------------- |
| `isolation` | `isolation`  | -              |

## Object Fit

Use the `objectFit` prop to set how an image or video should be resized to fit
its container.

```jsx
<Image src="..." objectFit="cover" width="10" height="10" />
```

| Prop        | CSS Property | Token Category |
| ----------- | ------------ | -------------- |
| `objectFit` | `object-fit` | -              |

## Object Position

Use the `objectPosition` prop to set how an element should be positioned within
its container.

```jsx
<Image src="..." objectPosition="center" width="10" height="10" />
```

| Prop             | CSS Property      | Token Category |
| ---------------- | ----------------- | -------------- |
| `objectPosition` | `object-position` | -              |

## Overflow

Use the `overflow` prop to control how content that exceeds an element's
dimensions is handled. This property determines whether to clip the content, add
scrollbars, or display the overflow content.

```jsx
<Box overflow="hidden" maxHeight="120px" />
<Box overflow="auto" maxHeight="120px" />
```

| Prop       | CSS Property | Token Category |
| ---------- | ------------ | -------------- |
| `overflow` | `overflow`   | -              |

## Overscroll Behavior

Use the `overscrollBehavior` prop to control what the browser does when reaching
the boundary of a scrolling area.

```jsx
<Box maxHeight="120px" overscrollBehavior="contain" />
```

| Prop                 | CSS Property          | Token Category |
| -------------------- | --------------------- | -------------- |
| `overscrollBehavior` | `overscroll-behavior` | -              |

## Position

Use the `position` utilities to set the position of an element.

```jsx
<Box position="absolute" />
<Box pos="absolute" /> // shorthand
```

| Prop       | CSS Property | Token Category |
| ---------- | ------------ | -------------- |
| `position` | `position`   | -              |

## Top / Right / Bottom / Left

Use the `top`, `right`, `bottom` and `left` utilities to set the position of an
element.

```jsx
<Box position="absolute" top="0" left="0" />

// using spacing tokens
<Box position="absolute" top="4" />

// using hardcoded values
<Box position="absolute" top="100px" />
```

Use the logical equivalents of `inset{Start|End}` utilities to set the position
of an element based on the writing mode.

```jsx
<Box position="absolute" insetStart="0" />
```

| Prop                                      | CSS Property         | Token Category |
| ----------------------------------------- | -------------------- | -------------- |
| `top`                                     | `top`                | `spacing`      |
| `right`                                   | `right`              | `spacing`      |
| `bottom`                                  | `bottom`             | `spacing`      |
| `left`                                    | `left`               | `spacing`      |
| `start`, `insetStart`, `insetInlineStart` | `inset-inline-start` | `spacing`      |
| `end` , `insetEnd`, `insetInlineEnd`      | `inset-inline-end`   | `spacing`      |
| `insetX`, `insetInline`                   | `inset-inline`       | `spacing`      |
| `insetY`, `insetBlock`                    | `inset-inline`       | `spacing`      |

## Visibility

Use the `visibility` prop to control the visibility of an element.

```jsx
<Box visibility="hidden" />
```

| Prop         | CSS Property | Token Category |
| ------------ | ------------ | -------------- |
| `visibility` | `visibility` | -              |

## Z-Index

Use the `zIndex` prop to set the z-index of an element.

```jsx
// using hardcoded values
<Box zIndex="1" />

// using token
<Box zIndex="overlay" />
```

| Prop     | CSS Property | Token Category |
| -------- | ------------ | -------------- |
| `zIndex` | `z-index`    | `zIndices`     |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/list.mdx

```text
---
title: List
description: JSX style props for customizing list styles.
---

## List Style Type

Use the `listStyleType` property to set the type of the list marker.

```jsx
<Box as="ul" listStyleType="circle">
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
</Box>
```

| Prop            | CSS Property      | Token Category |
| --------------- | ----------------- | -------------- |
| `listStyleType` | `list-style-type` | -              |

## List Style Position

Use the `listStylePosition` property to set the position of the list marker.

```jsx
<Box as="ul" listStylePosition="inside">
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
</Box>
```

| Prop                | CSS Property          | Token Category |
| ------------------- | --------------------- | -------------- |
| `listStylePosition` | `list-style-position` | -              |

## List Style Image

Use the `listStyleImage` property to set the image of the list marker.

```jsx
<Box as="ul" listStyleImage="url(...)">
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
</Box>
```

| Prop             | CSS Property       | Token Category |
| ---------------- | ------------------ | -------------- |
| `listStyleImage` | `list-style-image` | `assets`       |

## Markers

Use the `_marker` property to set the marker of a list item.

```jsx /_marker/
<ul>
  <Box as="li" _marker={{ color: "red" }}>
    Item 1
  </Box>
  <Box as="li" _marker={{ color: "blue" }}>
    Item 2
  </Box>
  <Box as="li" _marker={{ color: "green" }}>
    Item 3
  </Box>
</ul>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/sizing.mdx

```text
---
title: Sizing
description: JSX style props for sizing an element
---

## Width

Use the `width` or `w` property to set the width of an element.

```jsx
// hardcoded values
<Box width="64px" />
<Box w="4rem" />

// token values
<Box width="5" />
<Box w="5" />
```

### Fractional width

Use can set fractional widths using the `width` or `w` property.

Values can be within the following ranges:

- Thirds: `1/3` to `2/3`
- Fourths: `1/4` to `3/4`
- Fifths: `1/5` to `4/5`
- Sixths: `1/6` to `5/6`
- Twelfths: `1/12` to `11/12`

```jsx
// half width
<Flex>
  <Box width="1/2" />
  <Box width="1/2" />
</Flex>

// thirds
<Flex>
  <Box width="1/3" />
  <Box width="2/3" />
</Flex>

// fourths
<Flex>
  <Box width="1/4" />
  <Box width="3/4" />
</Flex>

// fifths
<Flex>
  <Box width="1/5" />
  <Box width="4/5" />
</Flex>

// sixths
<Flex>
  <Box width="1/6" />
  <Box width="5/6" />
</Flex>

// twelfths
<Flex>
  <Box width="3/12" />
  <Box width="9/12" />
</Flex>
```

### Viewport width

Use the modern viewport width values `dvw`, `svw`, `lvw`.

> `dvw` maps to `100dvw`, `svw` maps to `100svw`, `lvw` maps to `100lvw`.

```jsx
<Box width="dvw" />
<Box w="dvw" /> // shorthand
```

| Prop         | CSS Property | Token Category |
| ------------ | ------------ | -------------- |
| `w`, `width` | `width`      | `sizes`        |

## Max width

Use the `maxWidth` or `maxW` property to set the maximum width of an element.

```jsx
// hardcoded values
<Box maxWidth="640px" />
<Box maxW="4rem" /> // shorthand

// token values
<Box maxWidth="xl" />
<Box maxW="2xl" /> // shorthand
```

| Prop               | CSS Property | Token Category |
| ------------------ | ------------ | -------------- |
| `maxW`, `maxWidth` | `max-width`  | `sizes`        |

## Min width

Use the `minWidth` or `minW` property to set the minimum width of an element.

```jsx
// hardcoded values
<Box minWidth="64px" />
<Box minW="4rem" /> // shorthand

// token values
<Box minWidth="8" />
<Box minW="10" /> // shorthand
```

| Prop               | CSS Property | Token Category |
| ------------------ | ------------ | -------------- |
| `w`, `width`       | `width`      | `sizing`       |
| `maxW`, `maxWidth` | `max-width`  | `sizing`       |
| `minW`, `minWidth` | `min-width`  | `sizing`       |

## Height

Use the `height` or `h` property to set the height of an element.

```jsx
// hardcoded values
<Box height="40px" />
<Box h="0.4rem" /> // shorthand

// token values
<Box height="5" />
<Box h="5" /> // shorthand
```

### Fractional height

Use can set fractional heights using the `height` or `h` property.

Values can be within the following ranges:

- Thirds: `1/3` to `2/3`
- Fourths: `1/4` to `3/4`
- Fifths: `1/5` to `4/5`
- Sixths: `1/6` to `5/6`

```jsx
<Box height="1/2" />
<Box h="1/2" /> // shorthand
```

### Relative heights

Use the modern relative height values `dvh`, `svh`, `lvh`.

> `dvh` maps to `100dvh`, `svh` maps to `100svh`, `lvh` maps to `100lvh`.

```jsx
<Box height="dvh" />
<Box h="dvh" /> // shorthand
```

## Max height

Use the `maxHeight` or `maxH` property to set the maximum height of an element.

```jsx
// hardcoded values
<Box maxHeight="40px" />
<Box maxH="0.4rem" /> // shorthand

// token values
<Box maxHeight="8" />
<Box maxH="10" /> // shorthand
```

## Min height

Use the `minHeight` or `minH` property to set the minimum height of an element.

```jsx
// hardcoded values
<Box minHeight="40px" />
<Box minH="0.4rem" /> // shorthand

// token values
<Box minHeight="8" />
<Box minH="10" /> // shorthand
```

| Prop                | CSS Property | Token Category |
| ------------------- | ------------ | -------------- |
| `h`, `height`       | `height`     | `sizes`        |
| `maxH`, `maxHeight` | `max-height` | `sizes`        |
| `minH`, `minHeight` | `min-height` | `sizes`        |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/spacing.mdx

```text
---
title: Spacing
description:
  JSX style props for controlling the padding and margin of an element.
---

## Padding

### All sides

Use the `padding` prop to apply padding on all sides of an element

```jsx
// raw value
<Box padding="40px" />
<Box p="40px" /> // shorthand

// token value
<Box padding="4" />
<Box p="4" /> // shorthand
```

| Prop          | CSS Property | Token Category |
| ------------- | ------------ | -------------- |
| `p`,`padding` | `padding`    | `spacing`      |

### Specific side

Use the `padding{Left,Right,Top,Bottom}` to apply padding on one side of an
element

```jsx
<Box paddingLeft="3" />
<Box pl="3" /> // shorthand

<Box paddingTop="3" />
<Box pt="3" /> // shorthand
```

Use the `padding{Start,End}` props to apply padding on the logical axis of an
element based on the text direction.

```jsx
<div className={css({ paddingStart: '8' })} />
<div className={css({ ps: '8' })} /> // shorthand

<div className={css({ paddingEnd: '8' })} />
<div className={css({ pe: '8' })} /> // shorthand
```

| Prop                  | CSS Property           | Token Category |
| --------------------- | ---------------------- | -------------- |
| `pl`, `paddingLeft`   | `padding-left`         | `spacing`      |
| `pr`, `paddingRight`  | `padding-right`        | `spacing`      |
| `pt`, `paddingTop`    | `padding-top`          | `spacing`      |
| `pb`, `paddingBottom` | `padding-bottom`       | `spacing`      |
| `ps`, `paddingStart`  | `padding-inline-start` | `spacing`      |
| `pe`, `paddingEnd`    | `padding-inline-end`   | `spacing`      |

### Horizontal and Vertical padding

Use the `padding{X,Y}` props to apply padding on the horizontal and vertical
axis of an element

```jsx
<Box paddingX="8" />
<Box px="8" /> // shorthand

<Box paddingY="8" />
<Box py="8" /> // shorthand
```

| Prop                  | CSS Property           | Token Category |
| --------------------- | ---------------------- | -------------- |
| `p`,`padding`         | `padding`              | `spacing`      |
| `pl`, `paddingLeft`   | `padding-left`         | `spacing`      |
| `pr`, `paddingRight`  | `padding-right`        | `spacing`      |
| `pt`, `paddingTop`    | `padding-top`          | `spacing`      |
| `pb`, `paddingBottom` | `padding-bottom`       | `spacing`      |
| `px`, `paddingX`      | `padding-inline`       | `spacing`      |
| `py`, `paddingY`      | `padding-block`        | `spacing`      |
| `ps`, `paddingStart`  | `padding-inline-start` | `spacing`      |
| `pe`, `paddingEnd`    | `padding-inline-end`   | `spacing`      |

## Margin

### All sides

Use the `margin` prop to apply margin on all sides of an element

```jsx
<Box margin="5" />
<Box m="5" /> // shorthand
```

| Prop         | CSS Property | Token Category |
| ------------ | ------------ | -------------- |
| `m`,`margin` | `margin`     | `spacing`      |

### Specific side

Use the `margin{Left,Right,Top,Bottom}` to apply margin on one side of an
element

```jsx
<Box marginLeft="3" />
<Box ml="3" /> // shorthand

<Box marginTop="3" />
<Box mt="3" /> // shorthand
```

Use the `margin{Start,End}` properties to apply margin on the logical axis of an
element based on the text direction.

```jsx
<Box marginStart="8" />
<Box ms="8" /> // shorthand

<Box marginEnd="8" />
<Box me="8" /> // shorthand
```

| Prop                 | CSS Property          | Token Category |
| -------------------- | --------------------- | -------------- |
| `ml`, `marginLeft`   | `margin-left`         | `spacing`      |
| `mr`, `marginRight`  | `margin-right`        | `spacing`      |
| `mt`, `marginTop`    | `margin-top`          | `spacing`      |
| `mb`, `marginBottom` | `margin-bottom`       | `spacing`      |
| `ms`, `marginStart`  | `margin-inline-start` | `spacing`      |
| `me`, `marginEnd`    | `margin-inline-end`   | `spacing`      |

### Horizontal and Vertical margin

Use the `margin{X,Y}` properties to apply margin on the horizontal and vertical
axis of an element

```jsx
<Box marginX="8" />
<Box mx="8" /> // shorthand

<Box marginY="8" />
<Box my="8" /> // shorthand
```

| Prop            | CSS Property  | Token Category |
| --------------- | ------------- | -------------- |
| `mx`, `marginX` | `margin-left` | `spacing`      |
| `my`, `marginY` | `margin-top`  | `spacing`      |

## Spacing Between

Use the `space{X,Y}` props to apply spacing between elements. This approach uses
the owl selector `> * + *` to apply the spacing between the elements using
`margin*` properties.

:::info

It's recommended to use the `space` prop over the `gap` prop for spacing when
you need negative spacing.

:::

```jsx
<Box display="flex" spaceX="8">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>

<Box display="flex" spaceY="8">
  <Box>Item 1</Box>
  <Box>Item 2</Box>
  <Box>Item 3</Box>
</Box>
```

| Prop     | CSS Property          | Token Category |
| -------- | --------------------- | -------------- |
| `spaceX` | `margin-inline-start` | `spacing`      |
| `spaceY` | `margin-block-start`  | `spacing`      |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/svg.mdx

```text
---
title: SVG
description: JSX style props for SVG elements.
---

## Fill

Use the `fill` prop to set the fill color of an SVG element.

```jsx
<chakra.svg fill="blue.500">
  <path d="..." />
</chakra.svg>
```

| Prop   | CSS Property | Token Category |
| ------ | ------------ | -------------- |
| `fill` | `fill`       | `colors`       |

## Stroke

Use the `stroke` prop to set the stroke color of an SVG element.

```jsx
<chakra.svg stroke="blue.500">
  <path d="..." />
</chakra.svg>
```

| Prop     | CSS Property | Token Category |
| -------- | ------------ | -------------- |
| `stroke` | `stroke`     | `colors`       |

## Stroke Width

Use the `strokeWidth` prop to set the stroke width of an SVG element.

```jsx
<chakra.svg strokeWidth="1px">
  <path d="..." />
</chakra.svg>
```

| Prop          | CSS Property   | Token Category |
| ------------- | -------------- | -------------- |
| `strokeWidth` | `stroke-width` | `borderWidths` |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/tables.mdx

```text
---
title: Tables
description: JSX style props for styling table elements.
---

## Border Spacing

Control the border-spacing property of a table.

```jsx
<chakra.table borderSpacing="2">
  <tbody>
    <tr>
      <td>Cell 1</td>
      <td>Cell 2</td>
    </tr>
  </tbody>
</chakra.table>
```

| Prop            | CSS Property     | Token Category |
| --------------- | ---------------- | -------------- |
| `borderSpacing` | `border-spacing` | `spacing`      |

## Border Spacing X

Use the `borderSpacingX` prop to set the horizontal border-spacing property of a
table.

```jsx
<chakra.table borderSpacingX="2">
  <tbody>
    <tr>
      <td>Cell 1</td>
      <td>Cell 2</td>
    </tr>
  </tbody>
</chakra.table>
```

| Prop             | CSS Property     | Token Category |
| ---------------- | ---------------- | -------------- |
| `borderSpacingX` | `border-spacing` | `spacing`      |

## Border Spacing Y

Use the `borderSpacingY` prop to set the vertical border-spacing property of a
table.

```jsx
<chakra.table borderSpacingY="2">
  <tbody>
    <tr>
      <td>Cell 1</td>
      <td>Cell 2</td>
    </tr>
  </tbody>
</chakra.table>
```

| Prop             | CSS Property     | Token Category |
| ---------------- | ---------------- | -------------- |
| `borderSpacingY` | `border-spacing` | `spacing`      |

## Caption Side

Use the `captionSide` prop to set the side of the caption of a table.

```jsx
<table>
  <chakra.caption captionSide="bottom">This is a caption</chakra.caption>
  <tbody>
    <tr>
      <td>Cell 1</td>
      <td>Cell 2</td>
    </tr>
  </tbody>
</table>
```

| Prop          | CSS Property   | Token Category |
| ------------- | -------------- | -------------- |
| `captionSide` | `caption-side` | -              |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/transforms.mdx

```text
---
title: Transforms
description: JSX style props for transforming elements.
---

## Scale

Use the `scale` prop to control the scale of an element.

```jsx
<Box scale="1.2" />
```

When the `scale` prop is set to `auto`, the `scaleX` and `scaleY` props are used
to control the scale of the element.

| Prop    | CSS Property | Token Category |
| ------- | ------------ | -------------- |
| `scale` | `scale`      | -              |

## Scale X

Use the `scaleX` prop to control the scaleX property of an element. This
requires the `scale` prop to be set to `auto`.

```jsx
<Box scale="auto" scaleX="1.3" />
```

| Prop     | CSS Property | Token Category |
| -------- | ------------ | -------------- |
| `scaleX` | `--scale-x`  | -              |

## Scale Y

Use the `scaleY` prop to control the scaleY property of an element. This
requires the `scale` prop to be set to `auto`.

```jsx
<Box scale="auto" scaleY="0.4" />
```

| Prop     | CSS Property | Token Category |
| -------- | ------------ | -------------- |
| `scaleY` | `--scale-y`  | -              |

## Rotate

Use the `rotate` prop to control the rotate property of an element.

```jsx
<Box rotate="45deg" />
```

When the `rotate` prop is set to `auto`, the `rotateX` and `rotateY` props are
used to control the rotate of the element.

| Prop     | CSS Property | Token Category |
| -------- | ------------ | -------------- |
| `rotate` | `rotate`     | -              |

## Rotate X

Use the `rotateX` prop to control the rotateX property of an element.

```jsx
<Box rotateX="45deg" />
```

| Prop      | CSS Property | Token Category |
| --------- | ------------ | -------------- |
| `rotateX` | `--rotate-x` | -              |

## Rotate Y

Use the `rotateY` prop to control the rotateY property of an element.

```jsx
<Box rotateY="45deg" />
```

| Prop      | CSS Property | Token Category |
| --------- | ------------ | -------------- |
| `rotateY` | `--rotate-y` | -              |

## Translate

Use the `translate` prop to control the translate property of an element.

```jsx
<Box translate="40px" />
<Box translate="50% -40%" />
```

When the `translate` prop is set to `auto`, the `translateX` and `translateY`
props are used to control the translate of the element.

| Prop        | CSS Property | Token Category |
| ----------- | ------------ | -------------- |
| `translate` | `translate`  | -              |

## Translate X

Use the `translateX` prop to control the translateX property of an element. This
requires the `translate` prop to be set to `auto`.

```jsx
// hardcoded values
<Box translate="auto" translateX="50%" />
<Box translate="auto" translateX="20px" />

// token values
<Box translate="auto" translateX="4" />
<Box translate="auto" translateX="-10" />
```

| Prop         | CSS Property    | Token Category |
| ------------ | --------------- | -------------- |
| `translateX` | `--translate-x` | `spacing`      |

## Translate Y

Use the `translateY` prop to control the translateY property of an element. This
requires the `translate` prop to be set to `auto`.

```jsx
// hardcoded values
<Box translate="auto" translateY="-40%" />
<Box translate="auto" translateY="4rem" />

// token values
<Box translate="auto" translateY="4" />
<Box translate="auto" translateY="-10" />
```

| Prop         | CSS Property    | Token Category |
| ------------ | --------------- | -------------- |
| `translateY` | `--translate-y` | `spacing`      |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/transitions.mdx

```text
---
title: Transitions
description:
  JSX style props for controlling an element's transition and animation.
---

## Transition

Use the `transition` prop to control the transition of an element.

```jsx
// hardcoded value
<Box bg="red.400" _hover={{ bg: "red.500" }} transition="background 0.2s ease-in-out">
  Hover me
</Box>

// shortcut value
<Box bg="red.400" _hover={{ bg: "red.500" }} transition="backgrounds">
  Hover me
</Box>
```

| Prop         | CSS Property | Token Category |
| ------------ | ------------ | -------------- |
| `transition` | `transition` | -              |

## Transition Timing Function

Use the `transitionTimingFunction` prop to control the timing function of a
transition.

```jsx
<Box
  bg="red.400"
  _hover={{ bg: "red.500" }}
  transition="backgrounds"
  transitionTimingFunction="ease-in-out"
>
  Hover me
</Box>
```

| Prop                       | CSS Property                 | Token Category |
| -------------------------- | ---------------------------- | -------------- |
| `transitionTimingFunction` | `transition-timing-function` | `easings`      |

## Transition Duration

Use the `transitionDuration` prop to control the duration of a transition.

```jsx
<Box
  bg="red.400"
  _hover={{ bg: "red.500" }}
  transition="backgrounds"
  transitionDuration="fast"
>
  Hover me
</Box>
```

| Prop                 | CSS Property          | Token Category |
| -------------------- | --------------------- | -------------- |
| `transitionDuration` | `transition-duration` | `durations`    |

## Transition Delay

Use the `transitionDelay` prop to control the delay of a transition.

```jsx
<Box
  bg="red.400"
  _hover={{ bg: "red.500" }}
  transition="backgrounds"
  transitionDelay="fast"
>
  Hover me
</Box>
```

| Prop              | CSS Property       | Token Category |
| ----------------- | ------------------ | -------------- |
| `transitionDelay` | `transition-delay` | `durations`    |

## Animation

Use the `animation` prop to control the animation of an element.

```jsx
<Box animation="bounce" />
```

| Prop        | CSS Property     | Token Category |
| ----------- | ---------------- | -------------- |
| `animation` | `animation-name	` | `animations`   |

## Animation Name

Use the `animationName` prop to control the name of an animation. Compose
multiple animation names to create complex animations.

:::info

The value of the `animation` prop points to a global keyframe animation. Use the
`theme.keyframes` object to define the animation.

:::

```jsx
<Box animationName="bounce, fade-in" animationDuration="fast" />
```

| Prop            | CSS Property     | Token Category |
| --------------- | ---------------- | -------------- |
| `animationName` | `animation-name` | `animations`   |

## Animation Timing Function

Use the `animationTimingFunction` prop to control the timing function of an
animation.

```jsx
<Box animation="bounce" animationTimingFunction="ease-in-out" />
```

| Prop                      | CSS Property                | Token Category |
| ------------------------- | --------------------------- | -------------- |
| `animationTimingFunction` | `animation-timing-function` | `easings`      |

## Animation Duration

Use the `animationDuration` prop to control the duration of an animation.

```jsx
<Box animation="bounce" animationDuration="fast" />
```

| Prop                | CSS Property         | Token Category |
| ------------------- | -------------------- | -------------- |
| `animationDuration` | `animation-duration` | `durations`    |

## Animation Delay

Use the `animationDelay` prop to control the delay of an animation.

```jsx
<Box animation="bounce" animationDelay="fast" />
```

| Prop             | CSS Property      | Token Category |
| ---------------- | ----------------- | -------------- |
| `animationDelay` | `animation-delay` | `durations`    |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/style-props/typography.mdx

```text
---
title: Typography
description: JSX style props for styling text
---

## Font Family

Use the `fontFamily` prop to set the font family of a text element.

```jsx
<Text fontFamily="mono">Hello World</Text>
```

| Prop         | CSS Property  | Token Category |
| ------------ | ------------- | -------------- |
| `fontFamily` | `font-family` | `fonts`        |

## Font Size

Use the `fontSize` prop to set the font size of a text element.

```jsx
// hardcoded values
<Text fontSize="12px">Hello World</Text>
<Text fontSize="10rem">Hello World</Text>

// token values
<Text fontSize="xs">Hello World</Text>
<Text fontSize="4xl">Hello World</Text>
<Text fontSize="5xl">Hello World</Text>
```

| Prop       | CSS Property | Token Category |
| ---------- | ------------ | -------------- |
| `fontSize` | `font-size`  | `fonts`        |

## Text Styles

Use the `textStyle` prop to apply both a font size, line height, and letter
spacing composition at once.

```jsx
<Text textStyle="xs">Hello World</Text>
<Text textStyle="sm">Hello World</Text>
<Text textStyle="md">Hello World</Text>
<Text textStyle="lg">Hello World</Text>
<Text textStyle="xl">Hello World</Text>
<Text textStyle="2xl">Hello World</Text>
<Text textStyle="3xl">Hello World</Text>
<Text textStyle="4xl">Hello World</Text>
<Text textStyle="5xl">Hello World</Text>
```

| Prop        | Config             |
| ----------- | ------------------ |
| `textStyle` | `theme.textStyles` |

## Font Style

Use the `fontStyle` prop to set the font style of a text element.

```jsx
<Text fontStyle="italic">Hello World</Text>
```

| Prop        | CSS Property | Token Category |
| ----------- | ------------ | -------------- |
| `fontStyle` | `font-style` | none           |

## Font Weight

Use the `fontWeight` prop to set the font weight of a text element.

```jsx
// hardcoded values
<Text fontWeight="600">Hello World</Text>

// token values
<Text fontWeight="semibold">Hello World</Text>
```

| Prop         | CSS Property  | Token Category |
| ------------ | ------------- | -------------- |
| `fontWeight` | `font-weight` | `fontWeights`  |

## Font Variant Numeric

Use the `fontVariantNumeric` prop to set the font variant numeric of a text
element.

```jsx
<Text fontVariantNumeric="lining-nums">Hello World</Text>
```

| Prop                 | CSS Property           | Token Category |
| -------------------- | ---------------------- | -------------- |
| `fontVariantNumeric` | `font-variant-numeric` | none           |

## Letter Spacing

Use the `letterSpacing` prop to set the letter spacing of a text element.

```jsx
// hardcoded values
<Text letterSpacing="0.1rem">Hello World</Text>

// token values
<Text letterSpacing="tight">Hello World</Text>
<Text letterSpacing="wide">Hello World</Text>
<Text letterSpacing="wider">Hello World</Text>
<Text letterSpacing="widest">Hello World</Text>
```

| Prop            | CSS Property     | Token Category   |
| --------------- | ---------------- | ---------------- |
| `letterSpacing` | `letter-spacing` | `letterSpacings` |

## Truncation

Use the `truncate` prop to truncate text.

```jsx
<Text truncate>Lorem ipsum dolor sit amet...</Text>
```

| Prop       | CSS Property    | Token Category |
| ---------- | --------------- | -------------- |
| `truncate` | `text-overflow` | none           |

## Line Clamp

Use the `lineClamp` prop to truncate multi-line text. Set `lineClamp` to `none`
to disable truncation.

```jsx
<Text lineClamp="2">Lorem ipsum dolor sit amet...</Text>

// revert truncation
<Text lineClamp="none">Lorem ipsum dolor sit amet...</Text>
```

| Prop        | CSS Property        | Token Category |
| ----------- | ------------------- | -------------- |
| `lineClamp` | `webkit-line-clamp` | none           |

## Line Height

Use the `lineHeight` prop to set the line height of a text element.

```jsx
// hardcoded values
<Text lineHeight="1.5">Hello World</Text>

// token values
<Text lineHeight="tall">Hello World</Text>
```

| Prop         | CSS Property  | Token Category |
| ------------ | ------------- | -------------- |
| `lineHeight` | `line-height` | `lineHeights`  |

## Text Align

Use the `textAlign` prop to set the text alignment of a text element.

```jsx
<Text textAlign="left">Hello World</Text>
<Text textAlign="center">Hello World</Text>
<Text textAlign="right">Hello World</Text>
<Text textAlign="justify">Hello World</Text>
```

| Prop        | CSS Property | Token Category |
| ----------- | ------------ | -------------- |
| `textAlign` | `text-align` | none           |

## Text Color

Use the `color` prop to set the color of a text element.

```jsx
<Text color="red">Hello World</Text>
```

| Prop    | CSS Property | Token Category |
| ------- | ------------ | -------------- |
| `color` | `color`      | `colors`       |

## Text Decoration

Use the `textDecoration` or `textDecor` prop to set the text decoration of a
text element.

```jsx
<Text textDecoration="underline">Hello World</Text>
```

| Prop                          | CSS Property      | Token Category |
| ----------------------------- | ----------------- | -------------- |
| `textDecor`, `textDecoration` | `text-decoration` | none           |

## Text Decoration Color

Use the `textDecorationColor` prop to set the text decoration color of a text
element.

```jsx
<Text textDecoration="underline" textDecorationColor="red">
  Hello World
</Text>
```

| Prop                  | CSS Property            | Token Category |
| --------------------- | ----------------------- | -------------- |
| `textDecorationColor` | `text-decoration-color` | `colors`       |

## Text Decoration Style

Use the `textDecorationStyle` prop to set the text decoration style of a text
element.

```jsx
<Text textDecoration="underline" textDecorationStyle="dashed">
  Hello World
</Text>
```

| Prop                  | CSS Property            | Token Category |
| --------------------- | ----------------------- | -------------- |
| `textDecorationStyle` | `text-decoration-style` | none           |

## Text Decoration Thickness

Use the `textDecorationThickness` prop to set the text decoration thickness of a
text element.

```jsx
<Text textDecoration="underline" textDecorationThickness="1px">
  Hello World
</Text>
```

| Prop                      | CSS Property                | Token Category |
| ------------------------- | --------------------------- | -------------- |
| `textDecorationThickness` | `text-decoration-thickness` | none           |

## Text Underline Offset

Use the `textUnderlineOffset` prop to set the text underline offset of a text
element.

```jsx
<Text textDecoration="underline" textUnderlineOffset="1px">
  Hello World
</Text>
```

| Prop                  | CSS Property            | Token Category |
| --------------------- | ----------------------- | -------------- |
| `textUnderlineOffset` | `text-underline-offset` | none           |

## Text Transform

Use the `textTransform` prop to set the text transform of a text element.

```jsx
<Text textTransform="uppercase">Hello World</Text>
```

| Prop            | CSS Property     | Token Category |
| --------------- | ---------------- | -------------- |
| `textTransform` | `text-transform` | none           |

## Text Overflow

Use the `textOverflow` prop to set the text overflow of a text element.

```jsx
<Text textOverflow="ellipsis">Hello World</Text>
```

| Prop           | CSS Property    | Token Category |
| -------------- | --------------- | -------------- |
| `textOverflow` | `text-overflow` | none           |

## Text Shadow

Use the `textShadow` prop to set the text shadow of a text element.

```jsx
<Text textShadow="0 0 1px red">Hello World</Text>
```

| Prop         | CSS Property  | Token Category |
| ------------ | ------------- | -------------- |
| `textShadow` | `text-shadow` | `shadows`      |

## Text Indent

Use the `textIndent` prop to set the text indent of a text element.

```jsx
// hardcoded values
<Text textIndent="1rem">Hello World</Text>

// token values
<Text textIndent="3">Hello World</Text>
```

| Prop         | CSS Property  | Token Category |
| ------------ | ------------- | -------------- |
| `textIndent` | `text-indent` | `spacing`      |

## Vertical Align

Use the `verticalAlign` prop to set the vertical alignment of a text element.

```jsx
<Text verticalAlign="top">Hello World</Text>
```

| Prop            | CSS Property     | Token Category |
| --------------- | ---------------- | -------------- |
| `verticalAlign` | `vertical-align` | none           |

## White Space

Use the `whiteSpace` prop to set the white space of a text element.

```jsx
<Text whiteSpace="nowrap">Hello World</Text>
```

| Prop         | CSS Property  | Token Category |
| ------------ | ------------- | -------------- |
| `whiteSpace` | `white-space` | none           |

## Word Break

Use the `wordBreak` prop to set whether line breaks appear wherever the text
would otherwise overflow its content box.

```jsx
<Text wordBreak="break-all">Hello World</Text>
```

| Prop        | CSS Property | Token Category |
| ----------- | ------------ | -------------- |
| `wordBreak` | `word-break` | none           |

## Hyphens

Use the `hyphens` prop to set whether hyphens are used in the text.

```jsx
<Text hyphens="auto">Hello World</Text>
```

| Prop      | CSS Property | Token Category |
| --------- | ------------ | -------------- |
| `hyphens` | `hyphens`    | none           |
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/text-styles.mdx

```text
---
title: Text Styles
description:
  Learn how to use text styles to define typography related properties.
---

## Overview

Text styles allows you to define textual css properties. The common properties
are:

- **Font**: font family, weight, size
- **Line height**
- **Letter spacing**
- **Text decoration**
- **Text transform**

## Defining text styles

Text styles are defined using the `defineTextStyles` function.

```js filename="text-styles.ts"
import { defineTextStyles } from "@chakra-ui/react"

export const textStyles = defineTextStyles({
  body: {
    description: "The body text style - used in paragraphs",
    value: {
      fontFamily: "Inter",
      fontWeight: "500",
      fontSize: "16px",
      lineHeight: "24",
      letterSpacing: "0",
      textDecoration: "None",
      textTransform: "None",
    },
  },
})
```

## Built-in text styles

Chakra UI provides a set of built-in text styles.

<ExamplePreview name="tokens/text-styles" />

## Update the theme

To use the text styles, update the `theme` object with the `textStyles`
property.

```js filename="theme.ts"
import { createSystem, defineConfig } from "@chakra-ui/react"
import { textStyles } from "./text-styles"

const config = defineConfig({
  theme: {
    extend: {
      textStyles,
    },
  },
})

export default createSystem(defaultConfig, config)
```

After updating the theme, run this command to generate the typings.

```bash
npx @chakra-ui/cli typegen
```

## Using text styles

Now you can use `textStyle` property in your components.

```jsx
<Box textStyle="body">This is the body text style</Box>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/styling/virtual-color.mdx

```text
---
title: Virtual Color
description: Create color placeholders for better theming and customization.
---

## Overview

Chakra allows you to create a virtual color or color placeholder in your
project. The `colorPalette` property is how you create virtual color.

```js
<Box
  colorPalette="blue"
  bg={{ base: "colorPalette.100", _hover: "colorPalette.200" }}
>
  Hello World
</Box>
```

This will translate to the `blue.100` background color and `blue.200` background
color on hover.

## Usage

The fundamental requirement for virtual colors is that your colors must have a
consistent naming convention. By default, Chakra use `50-950` color values for
each color we provide.

This makes it easier for you to create and use virtual colors. Let's say we need
to create a themable outline button from scratch.

```jsx
<chakra.button
  borderWidth="1px"
  colorPalette="red"
  borderColor="colorPalette.500"
  _hover={{
    borderColor: "colorPalette.600",
  }}
>
  Click me
</chakra.button>
```

### Recipes

Virtual colors are most useful when used with recipes.

```js
const buttonRecipe = defineRecipe({
  base: {
    display: "flex",
    alignItems: "center",
    justifyContent: "center",
    // set the color palette
    colorPalette: "blue",
  },
  variants: {
    variant: {
      primary: {
        bg: "colorPalette.500",
        color: "white",
      },
      outline: {
        borderWidth: "1px",
        borderColor: "colorPalette.500",
        _hover: {
          borderColor: "colorPalette.600",
        },
      },
    },
  },
})
```

### Components

Most built-in components in Chakra support virtual colors.

```jsx
<Button colorPalette="blue">Click me</Button>
<Button colorPalette="red" variant="outline">
  Click me
</Button>
```

### Dark mode

Another amazing thing you can do with virtual colors is to use them with dark
mode.

```jsx
<Box
  colorPalette="blue"
  bg={{ base: "colorPalette.600", _dark: "colorPalette.400" }}
>
  Hello World
</Box>
```

> This element will have a `blue.600` background color in light mode and a
> `blue.400` background color in dark mode.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/animations.mdx

```text
---
title: Animations
description: The list of available animation tokens
hideToc: true
---

## Keyframes

Chakra UI supports the following keyframes out of the box.

<KeyframeDoc />

## Durations

Chakra UI supports the following durations out of the box.

<DurationTokenDoc />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/aspect-ratios.mdx

```text
---
title: Aspect Ratios
description: The list of available aspect ratios
hideToc: true
---

## Tokens

Chakra UI supports the following aspect ratios out of the box.

<AspectRatioTokenDoc />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/breakpoints.mdx

```text
---
title: Breakpoints
description: The list of available breakpoints
hideToc: true
---

Chakra UI supports the following breakpoints out of the box.

<BreakpointDoc />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/colors.mdx

```text
---
title: Colors
description: The list of available color tokens
hideToc: true
---

## Tokens

Chakra UI supports the following color tokens out of the box.

<ColorTokenDoc />

## Semantic Tokens

Chakra UI supports these semantic tokens out of the box.

:::info

In most cases, we recommend using semantic tokens.

:::

<ColorSemanticTokenDoc />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/cursors.mdx

```text
---
title: Cursors
description: The cursor tokens used for interactive elements.
---

## Overview

Chakra UI uses the `cursor` token to define the cursor for interactive elements.

<CursorTokenDoc />

## Cursor Tokens

To customize the cursor for interactive elements in Chakra, set the desired
`cursor` token values.

Here's a list of the available cursor tokens:

- **button**: Cursors for buttons
- **checkbox**: Cursors for checkbox and checkbox card
- **disabled**: Cursors for disabled elements
- **menuitem**: Cursors for menu item and menu option items.
- **option**: Cursors for select, combobox and listbox options
- **radio**: Cursors for radio and radio cards
- **slider**: Cursors for slider track and thumb interaction
- **switch**: Cursors for switch

## Customizing Cursors

Here's an example of how to change the cursor for a button, you can set the
`button` token to `default`.

```tsx
import { createSystem, defaultConfig } from "@chakra-ui/react"

export const system = createSystem(defaultConfig, {
  theme: {
    tokens: {
      cursor: {
        button: { value: "pointer" },
      },
    },
  },
})
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/customization/animations.mdx

```text
---
title: Animations
description: Learn how to customize animations and keyframes in Chakra UI
---

:::info

Please read the [overview](/docs/theming/customization/overview) first to learn
how to properly customize the styling engine, and get type safety.

:::

## Keyframes

Keyframes are used to define the animation sequence. Here's how to define custom
keyframes:

```tsx title="theme.ts"
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const config = defineConfig({
  theme: {
    keyframes: {
      shakeX: {
        "0%, 100%": { transform: "translateX(-100%)" },
        "50%": { transform: "translateX(100%)" },
      },
    },
  },
})

export const system = createSystem(defaultConfig, config)
```

## Animation Tokens

After defining keyframes, you can create animation tokens that reference them.
Animation tokens can include the keyframe name, duration, timing function, and
other animation properties.

```tsx title="theme.ts"
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const config = defineConfig({
  theme: {
    keyframes: {
      // ... keyframes from above
    },
    tokens: {
      animations: {
        shakeX: { value: "shakeX 1s ease-in-out infinite" },
      },
    },
  },
})

export const system = createSystem(defaultConfig, config)
```

## Usage

You can use the animation token directly in your component style props.

```tsx
<Box animation="shakeX" />
```

or as individual animation properties

```tsx
<Box
  animationName="shakeX"
  animationDuration="1s"
  animationTimingFunction="ease-in-out"
  animationIterationCount="infinite"
/>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/customization/breakpoints.mdx

```text
---
title: Breakpoints
description: Learn how to customize breakpoints in Chakra UI
---

:::info

Please read the [overview](/docs/theming/customization/overview) first to learn
how to properly customize the styling engine, and get type safety.

:::

## Example

Here's an example of how to customize breakpoints in Chakra UI.

```tsx title="theme.ts"
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const config = defineConfig({
  theme: {
    breakpoints: {
      tablet: "992px",
      desktop: "1200px",
      wide: "1400px",
    },
  },
})

export default createSystem(defaultConfig, config)
```

## Usage

When using responsive properties, reference the new breakpoints.

```tsx title="App.tsx"
<Box fontSize={{ base: "16px", tablet: "18px", desktop: "20px" }}>Hello</Box>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/customization/colors.mdx

```text
---
title: Colors
description: Learn how to customize colors in Chakra UI
---

:::info

Please read the [overview](/docs/theming/customization/overview) first to learn
how to properly customize the styling engine, and get type safety.

:::

## Tokens

To create new colors, we recommend providing `50` - `950` color values. Here's
an example of how to customize colors in Chakra UI.

```tsx title="theme.ts"
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const customConfig = defineConfig({
  theme: {
    tokens: {
      colors: {
        brand: {
          50: { value: "#e6f2ff" },
          100: { value: "#e6f2ff" },
          200: { value: "#bfdeff" },
          300: { value: "#99caff" },
          // ...
          950: { value: "#001a33" },
        },
      },
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

To use the `brand` color, you can set the value of any color related properties,
like `bg`, `borderColor`, `color`, etc. to the `brand` token.

```tsx
<Box bg="brand.100" />
```

## Semantic Tokens

### Color Palette

For new colors defined in the theme, we recommend creating these matching
semantic tokens to ensure consistency.

- `solid`: The bold fill color of the color.
- `contrast`: The text color that goes on solid color.
- `fg`: The foreground color used for text, icons, etc.
- `muted`: The muted color of the color.
- `subtle`: The subtle color of the color.
- `emphasized`: The emphasized version of the subtle color.
- `focusRing`: The focus ring color when interactive element is focused.

:::note

This is required if you intend to use the color in `colorPalette` property.

:::

```tsx title="theme.ts"
const customConfig = defineConfig({
  theme: {
    tokens: {
      colors: {
        brand: {
          // ...
        },
      },
    },
    semanticTokens: {
      colors: {
        brand: {
          solid: { value: "{colors.brand.500}" },
          contrast: { value: "{colors.brand.100}" },
          fg: { value: "{colors.brand.700}" },
          muted: { value: "{colors.brand.100}" },
          subtle: { value: "{colors.brand.200}" },
          emphasized: { value: "{colors.brand.300}" },
          focusRing: { value: "{colors.brand.500}" },
        },
      },
    },
  },
})
```

To use the color palette in components, you can use the `colorPalette` property.

```tsx
<Button colorPalette="brand">Click me</Button>
```

Alternative, you can also use the semantic token directly.

```tsx
<Box color="brand.contrast" bg="brand.solid">
  Hello world
</Box>
```

### Custom Tokens

Here's an example of how to create custom semantic tokens.

```tsx title="theme.ts"
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const customConfig = defineConfig({
  theme: {
    semanticTokens: {
      colors: {
        "checkbox-border": {
          value: { _light: "gray.200", _dark: "gray.800" },
        },
      },
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

Then, you can apply the `checkbox-border` token to any component.

```tsx
<Square size="4" borderColor="checkbox-border">
  <LuCheck />
</Square>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/customization/conditions.mdx

```text
---
title: Conditions
description: Learn how to customize conditions in Chakra UI
---

:::info

Please read the [overview](/docs/theming/customization/overview) first to learn
how to properly customize the styling engine, and get type safety.

:::

## Example

Here's an example of how to customize conditions in Chakra UI.

```tsx title="theme.ts"
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const customConfig = defineConfig({
  conditions: {
    off: "&:is([data-state=off])",
    on: "&:is([data-state=on])",
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

## Usage

Use `_off` and `_on` conditions to style elements based on the `data-state`
attribute.

```tsx title="app.tsx"
import { Box } from "@chakra-ui/react"

<Box data-state="off" _off={{ bg: "red.500" }} />
<Box data-state="on" _on={{ bg: "green.500" }} />
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/customization/css-variables.mdx

```text
---
title: CSS Variables
description: Learn how to customize CSS variables in Chakra UI
---

:::info

Please read the [overview](/docs/theming/customization/overview) first to learn
how to properly customize the styling engine, and get type safety.

:::

## Variable Root

Here's an example of how to customize the selector that token CSS variables are
applied to.

```tsx title="theme.ts"
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const customConfig = defineConfig({
  cssVarsRoot: ":where(html)",
})

export const system = createSystem(defaultConfig, customConfig)
```

The emitted CSS variables will now be applied to the `html` element.

```css
:where(html) {
  --chakra-colors-gray-100: #e6f2ff;
  --chakra-colors-gray-200: #bfdeff;
  --chakra-colors-gray-300: #99caff;
}
```

## Variable Prefix

Here's an example of how to customize the prefix of the emitted CSS variables.

```tsx title="theme.ts"
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const customConfig = defineConfig({
  cssVarsPrefix: "sui",
})

export const system = createSystem(defaultConfig, customConfig)
```

The emitted CSS variables will now use the `sui` prefix.

```css
:where(html) {
  --sui-colors-gray-100: #e6f2ff;
  --sui-colors-gray-200: #bfdeff;
  --sui-colors-gray-300: #99caff;
}
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/customization/global-css.mdx

```text
---
title: Global CSS
description: Learn how to customize global CSS in Chakra UI
---

:::info

Please read the [overview](/docs/theming/customization/overview) first to learn
how to properly customize the styling engine, and get type safety.

:::

## Example

Here's an example of how to customize the global CSS in Chakra UI.

```tsx title="theme.ts"
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const customConfig = defineConfig({
  globalCss: {
    "*::placeholder": {
      opacity: 1,
      color: "fg.subtle",
    },
    "*::selection": {
      bg: "green.200",
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

After customizing the global CSS, make sure to update your provider component to
use the new system.

```tsx title="components/ui/provider.tsx" /value={system}/
"use client"

import { system } from "@/components/theme"
import {
  ColorModeProvider,
  type ColorModeProviderProps,
} from "@/components/ui/color-mode"
import { ChakraProvider } from "@chakra-ui/react"

export function Provider(props: ColorModeProviderProps) {
  return (
    <ChakraProvider value={system}>
      <ColorModeProvider {...props} />
    </ChakraProvider>
  )
}
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/customization/overview.mdx

```text
---
title: Customization
description: Learn how to customize the Chakra UI theme
---

## Overview

Chakra UI uses a system of configs to define the default styling system.

- `defaultBaseConfig`: contains the conditions and style properties.
- `defaultConfig`: everything from `defaultBaseConfig` plus the built-in tokens
  and recipes.

The `defaultSystem` exported from Chakra UI uses the `defaultConfig` by default.

When customizing the theme, it's important to decide if you want to merge your
config with `defaultConfig` or start from scratch with `defaultBaseConfig`.

## Customization

These are the key functions needed to customize the Chakra UI theme.

- `defineConfig`: used to define the system config
- `mergeConfigs`: used to merge multiple system configs
- `createSystem`: used to create a styling engine from the config

```tsx title="theme.ts"
import { createSystem, defaultBaseConfig, defineConfig } from "@chakra-ui/react"

const customConfig = defineConfig({
  theme: {
    colors: {
      brand: {
        500: "tomato",
      },
    },
  },
})

export const system = createSystem(defaultBaseConfig, customConfig)
```

Next, update the `ChakraProvider` to use the custom system.

```tsx title="provider.tsx"
import { ChakraProvider } from "@chakra-ui/react"
import { ThemeProvider } from "next-themes"
import { system } from "./theme"

export function Provider(props: { children: React.ReactNode }) {
  return (
    <ChakraProvider value={system}>
      <ThemeProvider attribute="class" disableTransitionOnChange>
        {props.children}
      </ThemeProvider>
    </ChakraProvider>
  )
}
```

## Complete Customization

In most cases, we recommend starting with the default configuration and only
specify the things you want to customize.

However, if you prefer to start from scratch, scaffold the default tokens and
recipes using the CLI.

```bash
npx @chakra-ui/cli eject --outdir ./theme
```

This will generate a file that includes all the tokens and recipes in Chakra.

## TypeScript

After customizing the default config, you may need to update the types.

```bash
npx @chakra-ui/cli typegen ./theme.ts
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/customization/recipes.mdx

```text
---
title: Recipes
description: Learn how to customize recipes and slot recipes in Chakra UI
---

:::info

Please read the [overview](/docs/theming/customization/overview) first to learn
how to properly customize the styling engine, and get type safety.

:::

## Recipes

### Extending variants

Use the `defineRecipe` function to define a recipe override.

Here's an example of extending the `Button` to add a new `xl` size

```tsx title="theme.ts"
const buttonRecipe = defineRecipe({
  variants: {
    size: {
      xl: {
        fontSize: "lg",
        px: 6,
        py: 3,
      },
    },
  },
})

const customConfig = defineConfig({
  theme: {
    recipes: {
      button: buttonRecipe,
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

Then you can use the new size variant in your components.

```tsx
<Button size="xl">Click me</Button>
```

### Adding new variant

Use the `defineRecipe` function to define a new recipe variant.

Here's an example of defining a boolean variant called `raised`.

```tsx title="theme.ts"
const buttonRecipe = defineRecipe({
  variants: {
    raised: {
      true: {
        boxShadow: "md",
      },
    },
  },
})

const customConfig = defineConfig({
  theme: {
    recipes: {
      button: buttonRecipe,
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

Then you can use the new variant in your components.

```tsx
<Button raised>Click me</Button>
```

### Custom recipe

Use the `defineRecipe` function to define a custom recipe all together.

Here's an example of defining a custom recipe called `Title`

```tsx title="theme.ts"
const titleRecipe = defineRecipe({
  baseStyle: {
    fontWeight: "bold",
    letterSpacing: "tight",
  },
  variants: {
    size: {
      md: { fontSize: "xl" },
      lg: { fontSize: "2xl" },
    },
  },
})

const customConfig = defineConfig({
  theme: {
    recipes: {
      title: titleRecipe,
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

Then, use the new recipe to create a components

```tsx
const Title = (props) => {
  const recipe = useRecipe({ key: "title" })
  const styles = recipe({ size: "lg" })
  return <Box as="h1" css={styles} {...props} />
}
```

## Slot Recipes

To effectively override an existing slot recipe, we recommend connecting to its
anatomy. Slot recipes are added to the `theme.slotRecipes` object.

### Extending variants

Here's an example of how to extend the `Alert` slot recipe to create an `xl`
size.

```tsx title="theme.ts"
import { alertAnatomy } from "@chakra-ui/react/anatomy"

const alertSlotRecipe = defineSlotRecipe({
  slots: alertAnatomy.keys(),
  variants: {
    size: {
      xl: {
        root: {
          fontSize: "lg",
          px: 6,
          py: 3,
        },
      },
    },
  },
})

const customConfig = defineConfig({
  theme: {
    slotRecipes: {
      alert: alertSlotRecipe,
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

Then you can use the new size variant in your components.

```tsx /size="xl"/
<Alert size="xl" title="..." />
```

### Adding new variant

Here's an example of how to extend the `Alert` slot recipe to add a new variant
called `shape`.

```tsx title="theme.ts"
import { alertAnatomy } from "@chakra-ui/react/anatomy"

const alertSlotRecipe = defineSlotRecipe({
  slots: alertAnatomy.keys(),
  variants: {
    shape: {
      rounded: {
        root: { borderRadius: "full" },
      },
    },
  },
})

const customConfig = defineConfig({
  theme: {
    slotRecipes: {
      alert: alertSlotRecipe,
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

Then you can use the new variant in your components.

```tsx /shape="rounded"/
<Alert shape="rounded" title="..." />
```

### Custom recipe

Here's an example of how to define a custom slot recipe called `Navbar`.

```tsx title="theme.ts"
const navbarSlotRecipe = defineSlotRecipe({
  slots: ["root", "badge", "icon"],
  base: {
    root: {
      bg: "blue.500",
      color: "white",
      px: 4,
      py: 2,
    },
    badge: {
      borderRadius: "full",
      px: 2,
      py: 1,
    },
  },
})

const customConfig = defineConfig({
  theme: {
    slotRecipes: {
      navbar: navbarSlotRecipe,
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

Then you can use the new recipe to create a components

```tsx
const Navbar = (props) => {
  const recipe = useSlotRecipe({ key: "navbar" })
  const styles = recipe()
  return (
    <Box css={styles.root}>
      {props.children}
      <Box css={styles.badge} />
      <Box css={styles.icon} />
    </Box>
  )
}
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/customization/sizes.mdx

```text
---
title: Sizes
description: Learn how to customize sizes in Chakra UI
---

:::info

Please read the [overview](/docs/theming/customization/overview) first to learn
how to properly customize the styling engine, and get type safety.

:::

## Example

Here's an example of how to customize sizes in Chakra UI.

```tsx title="theme.ts"
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const customConfig = defineConfig({
  theme: {
    tokens: {
      sizes: {
        "1/7": { value: "14.285%" },
        "2/7": { value: "28.571%" },
        "3/7": { value: "42.857%" },
      },
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

## Usage

Set the value of any size related properties, like `width`, `height`,
`minWidth`, `minHeight`, `maxWidth`, `maxHeight`, etc. to the `sizes` token.

```tsx
<Box width="1/7" height="2/7" />
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/customization/spacing.mdx

```text
---
title: Spacing
description: Learn how to customize spacing in Chakra UI
---

:::info

Please read the [overview](/docs/theming/customization/overview) first to learn
how to properly customize the styling engine, and get type safety.

:::

## Example

Here's an example of how to customize spacing in Chakra UI.

```tsx title="theme.ts"
import { createSystem, defaultConfig, defineConfig } from "@chakra-ui/react"

const customConfig = defineConfig({
  theme: {
    tokens: {
      spacing: {
        "128": { value: "32rem" },
        "144": { value: "36rem" },
      },
    },
  },
})

export const system = createSystem(defaultConfig, customConfig)
```

## Usage

Here's how to use the custom spacing in Chakra UI.

```tsx
<Box margin="128" />
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/layer-styles.mdx

```text
---
title: Slot Recipes
description: The built-in layer styles in Chakra UI
---

Chakra UI provides these text styles out of the box.

<br />

<ExamplePreview name="tokens/layer-style" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/overview.mdx

```text
---
title: Overview
description: A guide for configuring the Chakra UI theming system.
---

## Architecture

The Chakra UI theming system is built around the API of
[Panda CSS](https://panda-css.com/).

Here's a quick overview of how the system is structured to provide a performant
and extensible styling system:

- Define the styling system configuration using the `defineConfig` function
- Create the styling engine using the `createSystem` function
- Pass the styling engine to the `ChakraProvider` component

```tsx
import { ChakraProvider, createSystem } from "@chakra-ui/react"

const config = defineConfig({
  theme: {
    tokens: {
      colors: {},
    },
  },
})

const system = createSystem(config)

export default function App() {
  return (
    <ChakraProvider value={system}>
      <Box>Hello World</Box>
    </ChakraProvider>
  )
}
```

## Config

The Chakra UI system is configured using the `defineConfig` function. This
function accepts a configuration object that allows you to customize the styling
system's behavior.

After a config is defined, it is passed to the `createSystem` function to create
the styling engine.

### cssVarsRoot

`cssVarsRoot` is the root element where the token CSS variables will be applied.

```tsx title="theme.ts"
const config = defineConfig({
  cssVarsRoot: ":where(:root, :host)",
})

export default createSystem(config)
```

### cssVarsPrefix

`cssVarsPrefix` is the prefix used for the token CSS variables.

```tsx title="theme.ts"
const config = defineConfig({
  cssVarsPrefix: "ck",
})

export default createSystem(config)
```

### globalCss

`globalCss` is used to apply global styles to the system.

```tsx title="theme.ts"
const config = defineConfig({
  globalCss: {
    "html, body": {
      margin: 0,
      padding: 0,
    },
  },
})

export default createSystem(config)
```

### theme

Use the `theme` config property to define the system theme. This property
accepts the following properties:

- `breakpoints`: for defining breakpoints
- `keyframes`: for defining css keyframes animations
- `tokens`: for defining tokens
- `semanticTokens`: for defining semantic tokens
- `textStyles`: for defining typography styles
- `layerStyles`: for defining layer styles
- `animationStyles`: for defining animation styles
- `recipes`: for defining component recipes
- `slotRecipes`: for defining component slot recipes

```tsx title="theme.ts"
const config = defineConfig({
  theme: {
    breakpoints: {
      sm: "320px",
      md: "768px",
      lg: "960px",
      xl: "1200px",
    },
    tokens: {
      colors: {
        red: "#EE0F0F",
      },
    },
    semanticTokens: {
      colors: {
        danger: { value: "{colors.red}" },
      },
    },
    keyframes: {
      spin: {
        from: { transform: "rotate(0deg)" },
        to: { transform: "rotate(360deg)" },
      },
    },
  },
})

export default createSystem(config)
```

### conditions

Use the `conditions` config property to define custom selectors and media query
conditions for use in the system.

```tsx title="theme.ts"
const config = defineConfig({
  conditions: {
    cqSm: "@container(min-width: 320px)",
    child: "& > *",
  },
})

export default createSystem(config)
```

Sample usage:

```tsx
<Box mt="40px" _cqSm={{ mt: "0px" }}>
  <Text>Hello World</Text>
</Box>
```

### strictTokens

Use the `strictTokens` config property to enforce the usage of only design
tokens. This will throw a TS error if you try to use a token that is not defined
in the theme.

```tsx title="theme.ts"
const config = defineConfig({
  strictTokens: true,
})

export default createSystem(config)
```

```tsx
// ❌ This will throw a TS error
<Box color="#4f343e">Hello World</Box>

// ✅ This will work
<Box color="red.400">Hello World</Box>
```

## TypeScript

When you configure the system properties (like `colors`, `space`, `fonts`,
etc.), the CLI can be used to generate type definitions for them.

```bash
npx @chakra-ui/cli typegen ./theme.ts
```

This will update the internal types in the `@chakra-ui/react` package, and make
sure they are in sync with the theme. Providing a type-safe API and delightful
experience for developers.

## System

After a config is defined, it is passed to the `createSystem` function to create
the styling engine. The returned `system` is framework-agnostic JavaScript
styling engine that can be used to style components.

```tsx
const system = createSystem(config)
```

The system includes the following properties:

### token

The token function is used to get a raw token value, or css variable.

```tsx
const system = createSystem(config)

// raw token
system.token("colors.red.200")
// => "#EE0F0F"

// token with fallback
system.token("colors.pink.240", "#000")
// => "#000"
```

Use the `token.var` function to get the css variable:

```tsx
// css variable
system.token.var("colors.red.200")
// => "var(--chakra-colors-red-200)"

// token with fallback
system.token.var("colors.pink.240", "colors.red.200")
// => "var(--chakra-colors-red-200)"
```

It's important to note that `semanticTokens` always return a css variable,
regardless of whether you use `token` or `token.var`. This is because semantic
tokens change based on the theme.

```tsx
// semantic token
system.token("colors.danger")
// => "var(--chakra-colors-danger)"

system.token.var("colors.danger")
// => "var(--chakra-colors-danger)"
```

### tokens

```tsx
const system = createSystem(config)

system.tokens.getVar("colors.red.200")
// => "var(--chakra-colors-red-200)"

system.tokens.expandReferenceInValue("3px solid {colors.red.200}")
// => "3px solid var(--chakra-colors-red-200)"

system.tokens.cssVarMap
// => Map { "colors": Map { "red.200": "var(--chakra-colors-red-200)" } }

system.tokens.flatMap
// => Map { "colors.red.200": "var(--chakra-colors-red-200)" }
```

### css

The `css` function is used to convert chakra style objects to CSS style object
that can be passed to `emotion` or `styled-components` or any other styling
library.

```tsx
const system = createSystem(config)

system.css({
  color: "red.200",
  bg: "blue.200",
})

// => { color: "var(--chakra-colors-red-200)", background: "var(--chakra-colors-blue-200)" }
```

### cva

The `cva` function is used to create component recipes. It returns a function
that, when called with a set of props, returns a style object.

```tsx
const system = createSystem(config)

const button = system.cva({
  base: {
    color: "white",
    bg: "blue.500",
  },
  variants: {
    outline: {
      color: "blue.500",
      bg: "transparent",
      border: "1px solid",
    },
  },
})

button({ variant: "outline" })
// => { color: "blue.500", bg: "transparent", border: "1px solid" }
```

### sva

The `sva` function is used to create component slot recipes. It returns a
function that, when called with a set of props, returns a style object for each
slot.

```tsx
const system = createSystem(config)

const alert = system.sva({
  slots: ["title", "description", "icon"],
  base: {
    title: { color: "white" },
    description: { color: "white" },
    icon: { color: "white" },
  },
  variants: {
    status: {
      info: {
        title: { color: "blue.500" },
        description: { color: "blue.500" },
        icon: { color: "blue.500" },
      },
    },
  },
})

alert({ status: "info" })
// => { title: { color: "blue.500" }, description: { color: "blue.500" }, icon: { color: "blue.500" } }
```

### isValidProperty

The `isValidProperty` function is used to check if a property is valid.

```tsx
const system = createSystem(config)

system.isValidProperty("color")
// => true

system.isValidProperty("background")
// => true

system.isValidProperty("invalid")
// => false
```

### splitCssProps

The `splitCssProps` function is used to split the props into css props and
non-css props.

```tsx
const system = createSystem(config)

system.splitCssProps({
  color: "red.200",
  bg: "blue.200",
  "aria-label": "Hello World",
})
// => [{ color: "red.200", bg: "blue.200" }, { "aria-label": "Hello World" }]
```

### breakpoints

The `breakpoints` property is used to query breakpoints.

```tsx
const system = createSystem(config)

system.breakpoints.up("sm")
// => "@media (min-width: 320px)"

system.breakpoints.down("sm")
// => "@media (max-width: 319px)"

system.breakpoints.only("md")
// => "@media (min-width: 320px) and (max-width: 768px)"

system.breakpoints.keys()
// => ["sm", "md", "lg", "xl"]
```

## Tokens

To learn more about tokens, please refer to the [tokens](/docs/theming/tokens)
section.

## Recipes

To learn more about recipes, please refer to the
[recipes](/docs/theming/recipes) section.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/radii.mdx

```text
---
title: Radii
description: The list of available border radius tokens
hideToc: true
---

## Tokens

Chakra UI supports the following border radius tokens out of the box.

<BorderRadiusTokenDoc />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/recipes.mdx

```text
---
title: Recipes
description: Writing multi-variant styles with recipes in Chakra.
---

## Overview

Chakra provides a way to write CSS-in-JS with better performance, developer
experience, and composability. One of its key features is the ability to create
multi-variant styles with a type-safe runtime API.

A recipe consists of these properties:

- `className`: The className to attach to the component
- `base`: The base styles for the component
- `variants`: The different visual styles for the component
- `compoundVariants`: The different combinations of variants for the component
- `defaultVariants`: The default variant values for the component

## Defining the recipe

Use the `defineRecipe` identity function to create a recipe.

```tsx title="button.recipe.ts"
import { defineRecipe } from "@chakra-ui/react"

export const buttonRecipe = defineRecipe({
  base: {
    display: "flex",
  },
  variants: {
    visual: {
      solid: { bg: "red.200", color: "white" },
      outline: { borderWidth: "1px", borderColor: "red.200" },
    },
    size: {
      sm: { padding: "4", fontSize: "12px" },
      lg: { padding: "8", fontSize: "24px" },
    },
  },
})
```

## Using the recipe

There are two ways to use the recipe in a component:

- Directly in the component with `useRecipe`
- Creating a component (recommended) with the `chakra` factory

:::info

**RSC Tip:** Adding the `"use client"` directive is required since it relies on
react hooks like `useContext` and `useInsertionEffect` under the hood.

:::

### Directly in component

Use the `useRecipe` hook to get the recipe for a component. Then, call the
recipe with its variant props to get the styles.

```tsx title="button.tsx" {9}
"use client"

import { chakra, useRecipe } from "@chakra-ui/react"
import { buttonRecipe } from "./button.recipe"

export const Button = (props) => {
  const { visual, size, ...restProps } = props

  const recipe = useRecipe({ recipe: buttonRecipe })
  const styles = recipe({ visual, size })

  return <chakra.button css={styles} {...restProps} />
}
```

#### splitVariantProps

Notice how the `visual` and `size` props were destructured from the props to be
passed to the recipe. A smarter approach would be to automatically split the
recipe props from the component props.

To do that, use the `recipe.splitVariantProps` function to split the recipe
props from the component props.

```tsx title="button.tsx" {8}
"use client"

import { chakra, useRecipe } from "@chakra-ui/react"
import { buttonRecipe } from "./button.recipe"

export const Button = (props) => {
  const recipe = useRecipe({ recipe: buttonRecipe })
  const [recipeProps, restProps] = recipe.splitVariantProps(props)
  const styles = recipe(recipeProps)

  // ...
}
```

#### TypeScript

To infer the recipe variant prop types, use the `RecipeVariantProps` type
helper.

```tsx title="button.tsx"
import type { RecipeVariantProps } from "@chakra-ui/react"
import { buttonRecipe } from "./button.recipe"

type ButtonVariantProps = RecipeVariantProps<typeof buttonRecipe>

export interface ButtonProps
  extends React.PropsWithChildren<ButtonVariantProps> {}
```

### Creating a component

Use the `chakra` function to create a component from a recipe.

> **Note:** The recipe can also be inlined into the `chakra` function.

```tsx title="button.tsx"
"use client"

import { chakra } from "@chakra-ui/react"
import { buttonRecipe } from "./button.recipe"

export const Button = chakra("button", buttonRecipe)
```

Next, use the component and pass recipe properties to it.

```tsx title="app.tsx"
import { Button } from "./button"

const App = () => {
  return (
    <Button visual="solid" size="lg">
      Click Me
    </Button>
  )
}
```

## Default Variants

The `defaultVariants` property is used to set the default variant values for the
recipe. This is useful when you want to apply a variant by default.

```tsx title="button.tsx" {19-22}
"use client"

import { chakra } from "@chakra-ui/react"

const Button = chakra("button", {
  base: {
    display: "flex",
  },
  variants: {
    visual: {
      solid: { bg: "red.200", color: "white" },
      outline: { borderWidth: "1px", borderColor: "red.200" },
    },
    size: {
      sm: { padding: "4", fontSize: "12px" },
      lg: { padding: "8", fontSize: "24px" },
    },
  },
  defaultVariants: {
    visual: "solid",
    size: "lg",
  },
})
```

## Compound Variants

Use the `compoundVariants` property to define a set of variants that are applied
based on a combination of other variants.

```tsx title="button.tsx" /compoundVariants/
"use client"

import { chakra } from "@chakra-ui/react"

const button = cva({
  base: {
    display: "flex",
  },
  variants: {
    visual: {
      solid: { bg: "red.200", color: "white" },
      outline: { borderWidth: "1px", borderColor: "red.200" },
    },
    size: {
      sm: { padding: "4", fontSize: "12px" },
      lg: { padding: "8", fontSize: "24px" },
    },
  },
  compoundVariants: [
    {
      size: "small",
      visual: "outline",
      css: {
        borderWidth: "2px",
      },
    },
  ],
})
```

When you use the `size="small"` and `visual="outline"` variants together, the
`compoundVariants` will apply the `css` property to the component.

```tsx title="app.tsx"
<Button size="small" visual="outline">
  Click Me
</Button>
```

## Theme Usage

To use the recipe in a reusable manner, move it to the system theme and add it
to `theme.recipes` property.

```tsx title="theme.ts"
import { createSystem, defineConfig } from "@chakra-ui/react"
import { buttonRecipe } from "./button.recipe"

const config = defineConfig({
  theme: {
    recipes: {
      button: buttonRecipe,
    },
  },
})

export default createSystem(config)
```

### TypeScript

Use the CLI to generate the types for the recipe.

```bash
npx @chakra-ui/cli typegen ./theme.ts
```

Then, import the generated types in your component.

```tsx title="button.tsx"
import type { RecipeVariantProps } from "@chakra-ui/react"
import { buttonRecipe } from "./button.recipe"

type ButtonVariantProps = RecipeVariantProps<typeof buttonRecipe>

export interface ButtonProps
  extends React.PropsWithChildren<ButtonVariantProps> {}
```

### Update code

If you use the recipe directly in your component, update the `useRecipe` to use
the `key` property to get the recipe from the theme.

```diff title="button.tsx"
const Button = () => {
-  const recipe = useRecipe({ recipe: buttonRecipe })
+  const recipe = useRecipe({ key: "button" })
  // ...
}
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/semantic-tokens.mdx

```text
---
title: Semantic Tokens
description: Leveraging semantic tokens for design decisions in your app.
---

## Overview

Semantic tokens are tokens that are designed to be used in a specific context. A
semantic token consists of the following properties:

- `value`: The value of the token or a reference to an existing token.
- `description`: An optional description of what the token can be used for.

## Defining Semantic Tokens

In most cases, the value of a semantic token references to an existing token.

> To reference a value in a semantic token, use the token reference `{}` syntax.

```js title="theme.ts"
import { createSystem, defineConfig } from "@chakra-ui/react"

const config = defineConfig({
  theme: {
    tokens: {
      colors: {
        red: { value: "#EE0F0F" },
      },
    },
    semanticTokens: {
      colors: {
        danger: { value: "{colors.red}" },
      },
    },
  },
})

export default createSystem(config)
```

## Using Semantic Tokens

After defining semantic tokens, we recommend using the Chakra CLI to generate
theme typings for your tokens.

```bash
npx @chakra-ui/cli typegen ./src/theme.ts
```

This will provide autocompletion for your tokens in your editor.

```tsx
<Box color="danger">Hello World</Box>
```

## Conditional Token

Semantic tokens can also be changed based on the conditions like light and dark
modes.

For example, if you want a color to change automatically based on light or dark
mode.

```js title="theme.ts"
import { createSystem, defineConfig } from "@chakra-ui/react"

const config = defineConfig({
  theme: {
    semanticTokens: {
      colors: {
        danger: {
          value: { base: "{colors.red}", _dark: "{colors.darkred}" },
        },
        success: {
          value: { base: "{colors.green}", _dark: "{colors.darkgreen}" },
        },
      },
    },
  },
})

export default createSystem(config)
```

:::info

The conditions used in semantic tokens must be an at-rule or parent selector
[condition](/docs/styling/conditional-styles#reference).

:::

## Semantic Token Nesting

Semantic tokens can be nested to create a hierarchy of tokens. This is useful
when you want to group tokens together.

:::info

Use the `DEFAULT` key to define the default value of a nested token.

:::

```js title="theme.ts"
import { createSystem, defineConfig } from "@chakra-ui/react"

const config = defineConfig({
  theme: {
    semanticTokens: {
      colors: {
        bg: {
          DEFAULT: { value: "{colors.gray.100}" },
          primary: { value: "{colors.teal.100}" },
          secondary: { value: "{colors.gray.100}" },
        },
      },
    },
  },
})

export default createSystem(config)
```

This allows the use of the `bg` token in the following ways:

```tsx
<Box bg="bg">
  <Box bg="bg.primary">Hello World</Box>
  <Box bg="bg.secondary">Hello World</Box>
</Box>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/shadows.mdx

```text
---
title: Shadows
description: The list of available shadow tokens
hideToc: true
---

## Semantic Tokens

Chakra UI supports these semantic tokens out of the box.

<ShadowTokenDoc />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/sizes.mdx

```text
---
title: Sizes
description: The list of available size tokens
hideToc: true
---

## Tokens

Chakra UI supports the following size tokens out of the box.

<SizesTokenDoc />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/slot-recipes.mdx

```text
---
title: Slot Recipes
description: Learn how to style multiple parts components with slot recipes.
---

## Overview

Slot Recipes come in handy when you need to apply style variations to multiple
parts of a component.

A slot recipe consists of these properties:

- `className`: The className prefix to attach to the component slot
- `slots`: An array of component parts to style
- `base`: The base styles per slot
- `variants`: The different visual styles for each slot
- `defaultVariants`: The default variant for the component
- `compoundVariants`: The compound variant combination and style overrides for
  each slot.

## Defining the recipe

Use the `defineSlotRecipe` identity function to create a slot recipe.

```tsx title="checkbox.recipe.ts"
import { defineSlotRecipe } from "@chakra-ui/react"

export const checkboxSlotRecipe = defineSlotRecipe({
  slots: ["root", "control", "label"],
  base: {
    root: { display: "flex", alignItems: "center", gap: "2" },
    control: { borderWidth: "1px", borderRadius: "sm" },
    label: { marginStart: "2" },
  },
  variants: {
    size: {
      sm: {
        control: { width: "8", height: "8" },
        label: { fontSize: "sm" },
      },
      md: {
        control: { width: "10", height: "10" },
        label: { fontSize: "md" },
      },
    },
  },
})
```

## Using the recipe

There are two ways to use the recipe in a component:

- Directly in the component with `useSlotRecipe`
- As a compound component (recommended) with `createSlotRecipeContext`

:::info

Adding the `"use client"` directive is required to use the `useSlotRecipe` hook
or `createSlotRecipeContext` function. This is because they rely on react hooks
like `useContext` and `useInsertionEffect` under the hood.

:::

### Directly in component

Use the `useSlotRecipe` hook to get the recipe for a component. Then, call the
recipe with its variant props to get the styles.

```tsx title="checkbox.tsx"
"use client"

import { chakra, useSlotRecipe } from "@chakra-ui/react"
import { checkboxSlotRecipe } from "./checkbox.recipe"

export const Checkbox = (props) => {
  const { size, ...restProps } = props

  const recipe = useSlotRecipe({ recipe: checkboxSlotRecipe })
  const styles = recipe({ size })

  return (
    <chakra.label css={styles.root}>
      <chakra.input type="checkbox" css={styles.control} {...restProps} />
      <chakra.span css={styles.label}>Checkbox Label</chakra.span>
    </chakra.label>
  )
}
```

#### splitVariantProps

Notice how the `size` prop was destructured from the props to be passed to the
recipe. A smarter approach would be to automatically split the recipe props from
the component props.

To do that, use the `recipe.splitVariantProps` function to split the recipe
props from the component props.

```tsx title="checkbox.tsx" {8}
"use client"

import { chakra, useSlotRecipe } from "@chakra-ui/react"
import { checkboxSlotRecipe } from "./checkbox.recipe"

export const Checkbox = (props) => {
  const recipe = useSlotRecipe({ recipe: checkboxSlotRecipe })
  const [recipeProps, restProps] = recipe.splitVariantProps(props)
  const styles = recipe(recipeProps)

  //...
}
```

#### TypeScript

To infer the recipe variant prop types, use the `RecipeVariantProps` type
helper.

```tsx title="checkbox.tsx"
import type { RecipeVariantProps } from "@chakra-ui/react"
import { checkboxSlotRecipe } from "./checkbox.recipe"

type CheckboxVariantProps = RecipeVariantProps<typeof checkboxSlotRecipe>

export interface CheckboxProps
  extends React.PropsWithChildren<CheckboxVariantProps> {}
```

### Create compound components

Pass the recipe to the `createSlotRecipeContext` function to create a slot
recipe context.

Then, use the `withProvider` and `withContext` functions to create the compound
components that share the same context.

:::info

You will need to manually type the generics for `withProvider` and
`withContext`. This approach is designed to optimize TypeScript performance.
Auto-inference, while convenient, would slow down TypeScript compilation due to
the complexity of the types involved.

:::

```tsx title="checkbox.tsx"
"use client"

import { createSlotRecipeContext } from "@chakra-ui/react"
import { checkboxSlotRecipe } from "./checkbox.recipe"

const { withProvider, withContext } = createSlotRecipeContext({
  recipe: checkboxSlotRecipe,
})

interface CheckboxRootProps
  extends HTMLChakraProps<
    "label",
    RecipeVariantProps<typeof checkboxSlotRecipe>
  > {}
export const CheckboxRoot = withProvider<HTMLLabelElement, CheckboxRootProps>(
  "label",
  "root",
)

interface CheckboxControlProps extends HTMLChakraProps<"input"> {}
export const CheckboxControl = withContext<
  HTMLInputElement,
  CheckboxControlProps
>("input", "control")

interface CheckboxLabelProps extends HTMLChakraProps<"span"> {}
export const CheckboxLabel = withContext<HTMLSpanElement, CheckboxLabelProps>(
  "span",
  "label",
)
```

Pass the variant props to the "root" component that to apply the styles.

> **Note:** The root component is the one that used the `withProvider` function.

```tsx title="app.tsx"
const App = () => {
  return (
    <CheckboxRoot size="md">
      <CheckboxControl />
      <CheckboxLabel />
    </CheckboxRoot>
  )
}
```

#### unstyled prop

This approach supports the use of the `unstyled` prop to remove the styles
applied by the recipe.

```tsx title="checkbox.tsx" /unstyled/
<CheckboxRoot unstyled>
  <CheckboxControl />
  <CheckboxLabel />
</CheckboxRoot>
```

#### TypeScript

To infer the recipe variant prop types, use the `RecipeVariantProps` type
helper.

```ts
import type { RecipeVariantProps, UnstyledProp } from "@chakra-ui/react"
import { checkboxSlotRecipe } from "./checkbox.recipe"

type CheckboxVariantProps = RecipeVariantProps<typeof checkboxSlotRecipe>

export interface CheckboxProps
  extends React.PropsWithChildren<CheckboxVariantProps>,
    UnstyledProp {}
```

## Compound Variants

Use the `compoundVariants` property to define a set of variants that are applied
based on a combination of other variants.

```tsx title="checkbox.recipe.ts" /compoundVariants/
import { defineSlotRecipe } from "@chakra-ui/react"

export const checkboxRecipe = defineSlotRecipe({
  slots: ["root", "control", "label"],
  base: {},
  variants: {
    size: {
      sm: {},
      md: {},
    },
    visual: {
      contained: {},
      outline: {},
    },
  },
  compoundVariants: [
    {
      size: "sm",
      visual: "outline",
      css: {
        control: { borderWidth: "1px" },
        label: { color: "green.500" },
      },
    },
  ],
})
```

## Targeting a slot

In some cases, targeting a slot by className might be needed.

- Set the `className` property in the config
- The naming convention is `${className}__${slot}`

```tsx title="checkbox.recipe.ts" /& .checkbox__label/
import { defineSlotRecipe } from "@chakra-ui/react"

export const checkboxRecipe = defineSlotRecipe({
  className: "checkbox",
  slots: ["root", "control", "label"],
  base: {
    root: {
      bg: "blue.500",
      _hover: {
        "& .checkbox__label": { color: "white" },
      },
    },
  },
})
```

## Theme Usage

To use the recipe in a reusable manner, move it to the system theme and add it
to `theme.slotRecipes` property.

> No need to add the `"use client"` directive when using the recipe in the
> theme.

```tsx title="theme.ts"
import { createSystem, defineConfig } from "@chakra-ui/react"
import { checkboxSlotRecipe } from "./checkbox.recipe"

const config = defineConfig({
  theme: {
    slotRecipes: {
      checkbox: checkboxSlotRecipe,
    },
  },
})

export default createSystem(config)
```

### TypeScript

Use the CLI to generate the types for the recipe.

```bash
npx @chakra-ui/cli typegen ./theme.ts
```

Then, import the generated types in your component.

```tsx title="checkbox.tsx"
import type { SlotRecipeProps, UnstyledProp } from "@chakra-ui/react"

export interface CheckboxProps
  extends SlotRecipeProps<"checkbox">,
    UnstyledProp {}
```

### Update code

If you use the recipe directly in your component, update the `useRecipe` to use
the `key` property to get the recipe from the theme.

```diff title="checkbox.tsx"
const Checkbox = () => {
-  const recipe = useRecipe({ recipe: checkboxRecipe })
+  const recipe = useRecipe({ key: "checkbox" })
  // ...
}
```

If you create a compound component, update the `createSlotRecipeContext` to use
the `key` property.

```diff title="checkbox.tsx"
const { withProvider, withContext } = createSlotRecipeContext({
-  recipe: checkboxRecipe,
+  key: "checkbox",
})
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/spacing.mdx

```text
---
title: Spacing
description: The list of available spacing tokens
hideToc: true
---

## Tokens

Chakra UI supports the following spacing tokens out of the box.

<SpacingTokenDoc />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/text-styles.mdx

```text
---
title: Text Styles
description: The built-in text styles in Chakra UI
---

Chakra UI provides these text styles out of the box.

<br />

<ExamplePreview name="tokens/text-styles" />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/tokens.mdx

```text
---
title: Tokens
description: Managing design decisions in your app using tokens.
---

## Overview

Design tokens are the platform-agnostic way to manage design decisions in your
application or website. It is a collection of attributes that describe any
fundamental/atomic visual style. Each attribute is a key-value pair.

> Design tokens in Chakra are largely influenced by the
> [W3C Token Format](https://tr.designtokens.org/format/).

A design token consists of the following properties:

- `value`: The value of the token. This can be any valid CSS value.
- `description`: An optional description of what the token can be used for.

## Defining Tokens

Tokens are defined in the under the `theme` key in your system config.

```ts title="theme.ts"
import { createSystem, defineConfig } from "@chakra-ui/react"

const config = defineConfig({
  theme: {
    tokens: {
      colors: {
        primary: { value: "#0FEE0F" },
        secondary: { value: "#EE0F0F" },
      },
      fonts: {
        body: { value: "system-ui, sans-serif" },
      },
    },
  },
})

export const system = createSystem(config)
```

:::warning

> Token values need to be nested in an object with a `value` key. This is to
> allow for additional properties like `description` and more in the future.

:::

## Using Tokens

After defining tokens, we recommend using the Chakra CLI to generate theme
typings for your tokens.

```bash
npx @chakra-ui/cli typegen ./src/theme.ts
```

This will provide autocompletion for your tokens in your editor.

```tsx
<Box color="primary" fontFamily="body">
  Hello World
</Box>
```

### Token reference syntax

Chakra UI enables you to reference design tokens within composite values for CSS
properties like `border`, `padding`, and `box-shadow`.  
This is achieved through the token reference syntax: `{path.to.token}`.

:::note

It is important to use the complete token path; for example, instead of using
`red.300`, you must reference it as `colors.red.300`.

:::

Here’s an example where token reference syntax is applied to both the border and
p (padding) props:

```tsx
<Box
  border="1px solid {colors.red.300}"
  p="{spacing.4} {spacing.6} {spacing.8} {spacing.10}"
  boxShadow="{spacing.4} {spacing.2} {spacing.2} {colors.red.300}"
/>
```

## Token Nesting

Tokens can be nested to create a hierarchy of tokens. This is useful when you
want to group related tokens together.

:::info

Use the `DEFAULT` key to define the default value of a nested token.

:::

```ts title="theme.ts"
import { defineConfig } from "@chakra-ui/react"

const config = defineConfig({
  theme: {
    tokens: {
      colors: {
        red: {
          DEFAULT: { value: "#EE0F0F" },
          100: { value: "#EE0F0F" },
        },
      },
    },
  },
})

export default createSystem(config)
```

```tsx
<Box
  // 👇🏻 This will use the `DEFAULT` value
  bg="red"
  color="red.100"
>
  Hello World
</Box>
```

## Token Types

### Colors

Colors have meaning and support the purpose of the content, communicating things
like hierarchy of information, and states. It is mostly defined as a string
value or reference to other tokens.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  colors: {
    red: {
      100: { value: "#fff1f0" },
    },
  },
})

export default createSystem({
  theme: { tokens },
})
```

### Gradients

Gradient tokens represent a smooth transition between two or more colors. Its
value can be defined as a string or a composite value.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  gradients: {
    // string value
    simple: { value: "linear-gradient(to right, red, blue)" },

    // composite value
    primary: {
      value: { type: "linear", placement: "to right", stops: ["red", "blue"] },
    },
  },
})

export default createSystem({
  theme: { tokens },
})
```

### Sizes

Size tokens represent the width and height of an element. Its value is defined
as a string.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  sizes: {
    sm: { value: "12px" },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Size tokens are typically used in `width`, `height`, `minWidth`, `maxWidth`,
> `minHeight`, `maxHeight` properties.

### Spacing

Spacing tokens represent the margin and padding of an element. Its value is
defined as a string.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  spacing: {
    gutter: { value: "12px" },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Spacing tokens are typically used in `margin`, `padding`, `gap`, and
> `{top,right,bottom,left}` properties.

### Fonts

Font tokens represent the font family of a text element. Its value is defined as
a string or an array of strings.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  fonts: {
    body: { value: "Inter, sans-serif" },
    heading: { value: ["Roboto Mono", "sans-serif"] },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Font tokens are typically used in `font-family` property.

### Font Sizes

Font size tokens represent the size of a text element. Its value is defined as a
string.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  fontSizes: {
    sm: { value: "12px" },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Font size tokens are typically used in `font-size` property.

### Font Weights

Font weight tokens represent the weight of a text element. Its value is defined
as a string.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  fontWeights: {
    bold: { value: "700" },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Font weight tokens are typically used in `font-weight` property.

### Letter Spacings

Letter spacing tokens represent the spacing between letters in a text element.
Its value is defined as a string.

```tsx
const tokens = defineTokens({
  letterSpacings: {
    wide: { value: "0.1em" },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Letter spacing tokens are typically used in `letter-spacing` property.

### Line Heights

Line height tokens represent the height of a line of text. Its value is defined
as a string.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  lineHeights: {
    normal: { value: "1.5" },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Line height tokens are typically used in `line-height` property.

### Radii

Radii tokens represent the radius of a border. Its value is defined as a string.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  radii: {
    sm: { value: "4px" },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Radii tokens are typically used in `border-radius` property.

### Borders

A border is a line surrounding a UI element. You can define them as string
values or as a composite value

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  borders: {
    // string value
    subtle: { value: "1px solid red" },
    // string value with reference to color token
    danger: { value: "1px solid {colors.red.400}" },
    // composite value
    accent: { value: { width: "1px", color: "red", style: "solid" } },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Border tokens are typically used in `border`, `border-top`, `border-right`,
> `border-bottom`, `border-left`, `outline` properties.

### Border Widths

Border width tokens represent the width of a border. Its value is defined as a
string.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  borderWidths: {
    thin: { value: "1px" },
    thick: { value: "2px" },
    medium: { value: "1.5px" },
  },
})

export default createSystem({
  theme: { tokens },
})
```

### Shadows

Shadow tokens represent the shadow of an element. Its value is defined as single
or multiple values containing a string or a composite value.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  shadows: {
    // string value
    subtle: { value: "0 1px 2px 0 rgba(0, 0, 0, 0.05)" },
    // composite value
    accent: {
      value: {
        offsetX: 0,
        offsetY: 4,
        blur: 4,
        spread: 0,
        color: "rgba(0, 0, 0, 0.1)",
      },
    },
    // multiple string values
    realistic: {
      value: [
        "0 1px 2px 0 rgba(0, 0, 0, 0.05)",
        "0 1px 4px 0 rgba(0, 0, 0, 0.1)",
      ],
    },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Shadow tokens are typically used in `box-shadow` property.

### Easings

Easing tokens represent the easing function of an animation or transition. Its
value is defined as a string or an array of values representing the cubic
bezier.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  easings: {
    // string value
    easeIn: { value: "cubic-bezier(0.4, 0, 0.2, 1)" },
    // array value
    easeOut: { value: [0.4, 0, 0.2, 1] },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Ease tokens are typically used in `transition-timing-function` property.

### Opacity

Opacity tokens help you set the opacity of an element.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  opacity: {
    50: { value: 0.5 },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Opacity tokens are typically used in `opacity` property.

### Z-Index

This token type represents the depth of an element's position on the z-axis.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  zIndex: {
    modal: { value: 1000 },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Z-index tokens are typically used in `z-index` property.

### Assets

Asset tokens represent a url or svg string. Its value is defined as a string or
a composite value.

```ts
type CompositeAsset = { type: "url" | "svg"; value: string }
type Asset = string | CompositeAsset
```

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  tokens: {
    assets: {
      logo: {
        value: { type: "url", value: "/static/logo.png" },
      },
      checkmark: {
        value: { type: "svg", value: "<svg>...</svg>" },
      },
    },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Asset tokens are typically used in `background-image` property.

### Durations

Duration tokens represent the length of time in milliseconds an animation or
animation cycle takes to complete. Its value is defined as a string.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  durations: {
    fast: { value: "100ms" },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Duration tokens are typically used in `transition-duration` and
> `animation-duration` properties.

### Animations

Animation tokens represent a keyframe animation. Its value is defined as a
string value.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  animations: {
    spin: {
      value: "spin 1s linear infinite",
    },
  },
})

export default createSystem({
  theme: { tokens },
})
```

> Animation tokens are typically used in `animation` property.

### Aspect Ratios

Aspect ratio tokens represent the aspect ratio of an element. Its value is
defined as a string.

```tsx title="theme.ts"
import { defineTokens } from "@chakra-ui/react"

const tokens = defineTokens({
  aspectRatios: {
    "1:1": { value: "1 / 1" },
    "16:9": { value: "16 / 9" },
  },
})

export default createSystem({
  theme: { tokens },
})
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/typography.mdx

```text
---
title: Typography
description: The list of available typography tokens
---

## Fonts

Here's the list of available fonts.

<FontTokenDoc />

## Font Sizes

Here's the list of available font sizes.

<FontSizeTokenDoc />

## Font Weights

Here's the list of available font weights.

<FontWeightTokenDoc />

## Line Heights

Here's the list of available line heights.

<LineHeightTokenDoc />

## Letter Spacings

Here's the list of available letter spacing.

<LetterSpacingTokenDoc />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/docs/theming/z-index.mdx

```text
---
title: Z-Index
description: The list of available z-index tokens
hideToc: true
---

## Tokens

Chakra UI supports the following z-index tokens out of the box.

<ZIndexTokenDoc />
```


## /home/donald/src/ext/chakra-ui/apps/www/content/guides/component-floating-input-label.mdx

```text
---
title: Add a Floating Label to an Input
description: To add a floating label to an input component in Chakra UI...
publishedAt: "2024-10-19"
collection: components
---

Floating labels conserve space in forms, particularly on mobile devices where
screen space is limited.

To add a floating label to an input component in Chakra UI, create a styled
input field using the `Field`, `Input`, and `Box` components.

<ExampleTabs name="input-with-floating-label" />

> For more details on Chakra UI's input component, refer to the
> [documentation](/docs/components/input).
```


## /home/donald/src/ext/chakra-ui/apps/www/content/guides/component-implement-context-menu.mdx

```text
---
title: Implement a Context Menu
description:
  Utilize the MenuContextTrigger component to create a context menu in Chakra UI
publishedAt: "2024-10-19"
collection: components
---

A context menu provides users with a convenient way to access actions related to
a specific UI element through a right-click.

Utilize the `MenuContextTrigger` component to create a context menu.

<ExampleTabs name="menu-with-context-trigger" />

> For more details on Chakra UI's menu components, refer to the
> [documentation](/docs/components/menu).
```


## /home/donald/src/ext/chakra-ui/apps/www/content/guides/snippet-how-to-manage-updates.mdx

```text
---
title: How to manage updates
description: Learn how to manage updates for component snippets
publishedAt: "2024-11-15"
collection: snippets
---

When snippets are updated from Chakra UI, we document them in the changelog so
you're aware.

In your project, it's up to you to decide whether to update the snippets or not.
In most cases, you can check the
[snippet directory](https://github.com/chakra-ui/chakra-ui/tree/main/apps/compositions/src/ui)
to see the changes.

When running the `npx chakra snippet add` command, the CLI will check for
existing snippets in your project and skip adding them if they already exist.

If you want to overwrite the existing snippets, run the following command:

```bash
npx chakra snippet add --force
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/guides/snippet-specify-custom-directory.mdx

```text
---
title: Specifying custom directory
description: Learn how to specify custom directories for component snippets
publishedAt: "2024-11-15"
collection: snippets
---

The generated snippets by the Chakra UI CLI are placed in the `components/ui`
directory by default.

You can specify a custom directory by running the CLI with the `--outdir` flag.

```bash
npx chakra snippets --outdir ./components/custom
```

> To learn more about the CLI snippets command, refer to the
> [CLI](/docs/get-started/cli) docs.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/guides/theming-add-custom-font-to-nextjs.mdx

```text
---
title: Add custom fonts to a Next.js project
description:
  Learn how to add a custom font when using Chakra UI in a Next.js project
publishedAt: "2024-11-18"
collection: theming
---

## Loading the custom fonts

Google Fonts are available in Next.js by default. At the top of your
`layout.tsx` file, import the font from `next/font/google`:

```tsx title="layout.tsx"
import { Bricolage_Grotesque } from "next/font/google"
```

Configure the font by defining the variable and subsets to include:

```tsx title="layout.tsx"
const bricolage = Bricolage_Grotesque({
  variable: "--font-bricolage",
  subsets: ["latin"],
})
```

Now, attach the font to the `<html>` element in your application. This ensures
that the font is available to be used in Chakra UI.

```tsx /className={bricolage.variable}/
<html className={bricolage.variable} lang="en" suppressHydrationWarning>
  <body>
    <Provider>{children}</Provider>
  </body>
</html>
```

## Configure the custom font

Use the `createSystem` method to define the custom font in Chakra UI's theme
configuration:

```tsx title="components/ui/provider.tsx"
"use client"

import { createSystem, defaultConfig } from "@chakra-ui/react"

const system = createSystem(defaultConfig, {
  theme: {
    tokens: {
      fonts: {
        heading: { value: "var(--font-bricolage)" },
        body: { value: "var(--font-bricolage)" },
      },
    },
  },
})
```

:::info

You can customize which text elements use the font by specifying it for
`heading`, `body`, or both. In this case, we are setting both the body and
heading fonts to "Bricolage Grotesque".

:::

Finally, pass the `system` into the `ChakraProvider`

```tsx title="components/ui/provider.tsx" /value={system}/
export function Provider(props: ColorModeProviderProps) {
  return (
    <ChakraProvider value={system}>
      <ColorModeProvider {...props} />
    </ChakraProvider>
  )
}
```

This ensures that the custom font is applied across your entire app.

> Remember to remove any unused styles from your `global.css` and
> `page.module.css` files to prevent your custom font from being overridden.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/guides/theming-add-custom-font-to-vite.mdx

```text
---
title: Add custom fonts to a Vite project
description:
  Learn how to add a custom font when using Chakra UI in a Vite project
publishedAt: "2024-11-18"
collection: theming
---

## Loading the custom fonts

To add a custom font in your project, install the Google font you want to use
from [Fontsource](https://fontsource.org/). For this guide, we'll use the
["Bricolage Grotesque"](https://fontsource.org/fonts/bricolage-grotesque) font
as an example.

```bash
pnpm add @fontsource-variable/bricolage-grotesque
```

Next, import the font at the root of your project, referencing the `css` path:

```tsx title="main.tsx"
import "@fontsource-variable/bricolage-grotesque/index.css"
```

## Configure the custom font

Use the `createSystem` method to define the custom font in Chakra UI's theme
configuration:

```tsx title="components/ui/provider.tsx"
"use client"

import { createSystem, defaultConfig } from "@chakra-ui/react"

const system = createSystem(defaultConfig, {
  theme: {
    tokens: {
      fonts: {
        heading: { value: "Bricolage Grotesque Variable" },
        body: { value: "Bricolage Grotesque Variable" },
      },
    },
  },
})
```

:::info

You can customize which text elements use the font by specifying it for
`heading`, `body`, or both. In this case, we are setting both the body and
heading fonts to "Bricolage Grotesque".

:::

Finally, pass the `system` into the `ChakraProvider`

```tsx title="components/ui/provider.tsx" /value={system}/
export function Provider(props: ColorModeProviderProps) {
  return (
    <ChakraProvider value={system}>
      <ColorModeProvider {...props} />
    </ChakraProvider>
  )
}
```

This ensures that the custom font is applied across your entire app.
```


## /home/donald/src/ext/chakra-ui/apps/www/content/guides/theming-change-default-color-palette.mdx

```text
---
title: Change the default color palette
description: Learn how to change the default color palette in Chakra UI
publishedAt: "2024-12-04"
collection: theming
---

By default, Chakra UI uses the `gray` color palette for various UI elements like
selection backgrounds and hover states. You can customize this default behavior
by modifying the global CSS configuration.

## Customizing the default color palette

Use the `createSystem` method to override the default color palette in your
theme configuration:

```tsx title="components/theme.ts"
const config = defineConfig({
  globalCss: {
    html: {
      colorPalette: "blue", // Change this to any color palette you prefer
    },
  },
})

export const system = createSystem(defaultConfig, config)
```

Next, add the new `system` to your `components/ui/provider.tsx` file:

```tsx /value={system}/
"use client"

import { system } from "@/components/theme"
import { ChakraProvider } from "@chakra-ui/react"

export function Provider(props: ColorModeProviderProps) {
  return (
    <ChakraProvider value={system}>
      <ColorModeProvider {...props} />
    </ChakraProvider>
  )
}
```

## What this affects

Changing the default color palette will affect various UI elements across your
application, including:

- Text selection background colors
- Default hover states
- Focus states
- Other interactive elements that use the default color palette

## Using custom color palettes

You can use any of the built-in color palettes or your own custom color palette:

```tsx
// Built-in color palettes
colorPalette: "blue"
colorPalette: "green"
colorPalette: "red"

// Custom color palette (if defined in your theme)
colorPalette: "brand"
```

:::warning

When using a custom color palette, make sure you've defined all the necessary
color tokens (50-900) and semantic tokens in your theme configuration.

:::

> For more information about creating custom color palettes, see the guide on
> [creating custom colors](/docs/guides/theming-custom-colors).
```


## /home/donald/src/ext/chakra-ui/apps/www/content/guides/theming-custom-breakpoints.mdx

```text
---
title: Creating custom breakpoints
description: Learn how to create custom breakpoints in Chakra UI
publishedAt: "2024-11-15"
collection: theming
---

Custom breakpoints are defined in the `breakpoints` property of your theme.

```tsx title="components/theme.ts" /breakpoints: {/
const config = defineConfig({
  theme: {
    breakpoints: {
      xl: "80em" ,
      "2xl": "96em" ,
      "3xl": "120em" ,
      "4xl": "160em" ,
    },
  },
})

export const system = createSystem(defaultConfig, config)
```

Next, you add the new `system` to your `components/ui/provider.tsx` files

```tsx {3} /value={system}/
"use client"

import { system } from "@/components/theme"
import {
  ColorModeProvider,
  type ColorModeProviderProps,
} from "@/components/ui/color-mode"
import { ChakraProvider } from "@chakra-ui/react"

export function Provider(props: ColorModeProviderProps) {
  return (
    <ChakraProvider value={system}>
      <ColorModeProvider {...props} />
    </ChakraProvider>
  )
}
```

Next, you run the CLI `typegen` command to generate the types.

```bash
npx chakra typegen ./components/theme.ts
```

> Note: You might need to restart your TypeScript server for the types to be
> picked up.

## Using the breakpoint

With that in place, you can use the breakpoints when writing responsive styles.

```tsx /"4xl"/
<Box fontSize={{ base: "sm", "4xl": "lg" }}>Hello world</Box>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/guides/theming-custom-colors.mdx

```text
---
title: Creating custom colors
description: Learn how to create custom colors in Chakra UI
publishedAt: "2024-11-15"
collection: theming
---

To create custom colors in Chakra UI, you want to define two things:

- **tokens**: For the 50-950 color palette
- **semanticTokens**: For the `solid`, `contrast`, `fg`, `muted`, `subtle`,
  `emphasized`, and `focusRing` color keys

> To learn more about tokens and semantic tokens, refer to the
> [theming guide](/docs/theming/customization/colors#semantic-tokens).

```tsx title="components/theme.ts" /brand: {/
const config = defineConfig({
  theme: {
    tokens: {
      colors: {
        brand: {
          50: { value: "#e6f2ff" },
          100: { value: "#e6f2ff" },
          200: { value: "#bfdeff" },
          300: { value: "#99caff" },
          // ...
          950: { value: "#001a33" },
        },
      },
    },
    semanticTokens: {
      colors: {
        brand: {
          solid: { value: "{colors.brand.500}" },
          contrast: { value: "{colors.brand.100}" },
          fg: { value: "{colors.brand.700}" },
          muted: { value: "{colors.brand.100}" },
          subtle: { value: "{colors.brand.200}" },
          emphasized: { value: "{colors.brand.300}" },
          focusRing: { value: "{colors.brand.500}" },
        },
      },
    },
  },
})

export const system = createSystem(defaultConfig, config)
```

Next, you add the new `system` to your `components/ui/provider.tsx` files

```tsx /value={system}/
"use client"

import { system } from "@/components/theme"
import {
  ColorModeProvider,
  type ColorModeProviderProps,
} from "@/components/ui/color-mode"
import { ChakraProvider } from "@chakra-ui/react"

export function Provider(props: ColorModeProviderProps) {
  return (
    <ChakraProvider value={system}>
      <ColorModeProvider {...props} />
    </ChakraProvider>
  )
}
```

Next, you run the CLI `typegen` command to generate the types.

```bash
npx chakra typegen ./theme.ts
```

## Using the color

With that in place, you can use the color in components as well as
`colorPalette` utilities.

```tsx /brand/
<Button colorPalette="brand">Click me</Button>
```

You can also use the semantic token directly.

```tsx /brand./
<Box color="brand.contrast" bg="brand.solid">
  Hello world
</Box>
```

Alternatively, you can use the raw token value.

```tsx /brand.500/
<Box color="brand.500">Hello world</Box>
```
```


## /home/donald/src/ext/chakra-ui/apps/www/content/guides/theming-customize-dark-mode-colors.mdx

```text
---
title: Customize dark mode colors
description:
  Learn how to customize the entire theme colors for dark mode in Chakra UI
publishedAt: "2024-12-05"
collection: theming
---

You can customize the dark mode colors in Chakra UI by modifying the `_dark`
values of the semantic color tokens. This guide will show you how to override
the default dark mode colors.

## Customizing dark mode colors

Use the `createSystem` method to override the dark mode colors in your theme
configuration:

```tsx title="components/theme.ts" /_dark/
const config = defineConfig({
  theme: {
    semanticTokens: {
      colors: {
        bg: {
          DEFAULT: {
            value: { _light: "{colors.white}", _dark: "#141414" }, // Custom dark background
          },
          subtle: {
            value: { _light: "{colors.gray.50}", _dark: "#1a1a1a" }, // Custom dark subtle background
          },
          muted: {
            value: { _light: "{colors.gray.100}", _dark: "#262626" }, // Custom dark muted background
          },
        },
        fg: {
          DEFAULT: {
            value: { _light: "{colors.black}", _dark: "#e5e5e5" }, // Custom dark text color
          },
          muted: {
            value: { _light: "{colors.gray.600}", _dark: "#a3a3a3" }, // Custom dark muted text
          },
        },
        border: {
          DEFAULT: {
            value: { _light: "{colors.gray.200}", _dark: "#404040" }, // Custom dark border
          },
        },
      },
    },
  },
})

export const system = createSystem(defaultConfig, config)
```

:::info

To see the full list of semantic color tokens, see the
[semantic tokens source code](https://github.com/chakra-ui/chakra-ui/blob/main/packages/react/src/theme/semantic-tokens/colors.ts).

:::

Next, add the new `system` to your `components/ui/provider.tsx` file:

```tsx /value={system}/
"use client"

import { system } from "@/components/theme"
import { ChakraProvider } from "@chakra-ui/react"

export function Provider(props: ColorModeProviderProps) {
  return (
    <ChakraProvider value={system}>
      <ColorModeProvider {...props} />
    </ChakraProvider>
  )
}
```

## Understanding semantic color tokens

The semantic color tokens in Chakra UI follow this pattern:

```tsx
{
  [colorKey]: {
    [variant]: {
      value: {
        _light: string, // Light mode value
        _dark: string   // Dark mode value
      }
    }
  }
}
```

Common color keys include:

- `bg` - Background colors
- `fg` - Foreground/text colors
- `border` - Border colors

Each color key has variants like:

- `DEFAULT` - Base color
- `subtle` - Subtle variation
- `muted` - Muted variation
- `emphasized` - Emphasized variation

## Customizing specific color palettes

You can also customize specific color palettes for dark mode:

```tsx
const config = defineConfig({
  theme: {
    semanticTokens: {
      colors: {
        blue: {
          solid: {
            value: { _light: "{colors.blue.600}", _dark: "#0284c7" }, // Custom dark blue
          },
          muted: {
            value: { _light: "{colors.blue.100}", _dark: "#082f49" }, // Custom dark muted blue
          },
        },
        // Add more color palettes as needed
      },
    },
  },
})
```

## Best practices

1. **Color contrast**: Ensure your custom dark mode colors maintain sufficient
   contrast for accessibility.

2. **Consistent palette**: Keep your dark mode colors consistent across your
   application by using a cohesive color palette.

3. **Testing**: Always test your custom colors in both light and dark modes to
   ensure good readability and visual harmony.

> For more information about semantic tokens, see the
> [semantic tokens guide](/docs/theming/semantic-tokens).
```


## /home/donald/src/ext/chakra-ui/apps/www/content/notes/mdx-reference.mdx

```text
---
id: getting-started
title: Getting Started
description: Learn how to get started with the Chakra UI v3.0
---

# Getting Started

> Blockquotes are very `handy` in email to emulate reply text. This line is part
> of the same quote.

Lorem ipsum **dolor** sit amet, consectetur adipiscing elit. Fusce vestibulum
ante non neque convallis tempor. Pellentesque habitant morbi tristique senectus
et.

- first item
- second item
- second item
- second item

::::card-group

:::card{href="#" icon="nextjs"}

### Hello

Discover all the components available in Chakra UI.

:::

:::card{href="#" icon="terminal"}

### Hello

Discover all the components available in Chakra UI.

:::

::::

## Some snippets

```js
import { Accordion } from "@chakra-ui/react"
```

---

| Syntax    | Description |
| --------- | ----------- |
| Header    | Title       |
| Paragraph | Text        |

:::note

This is a note callout. [Link](https://google.com)

:::

:::info

This is an info callout. [Link](https://google.com)

:::

```bash [Terminal]
npm i @chakra-ui/react
```

:::code-group

```bash [npm]
npm i @chakra-ui/react
```

```bash [pnpm]
pnpm i @chakra-ui/react
```

```bash [bun]
bun i @chakra-ui/react
```

:::

::::steps

### Step one

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce vestibulum ante
non neque convallis tempor. Pellentesque habitant morbi tristique senectus et
netus et malesuada fames ac turpis egestas. Nam a iaculis libero.

### Step two

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce vestibulum ante
non neque convallis tempor. Pellentesque habitant morbi tristique senectus et
netus et malesuada fames ac turpis egestas. Nam a iaculis libero.

:::code-group

```tsx [console.log]
console.log("hello world")
```

```tsx [alert]
alert("hello world")
```

:::

:::info

test

:::

### Step three

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce vestibulum ante
non neque convallis tempor. Pellentesque habitant morbi tristique senectus et
netus et malesuada fames ac turpis egestas. Nam a iaculis libero.

::::

```ts
console.log("hewwo") // [!code --]
console.log("hello") // [!code ++]
console.log("goodbye")
```

```ts {1}
console.log("hewwo")
console.log("goodbye")
```

```ts
// [!code word:Hello]
const message = "Hello World"
```

<Example name="breadcrumb-basic" />
```

