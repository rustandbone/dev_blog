---
layout: post
title: "230828 월 멋쟁이사자처럼 React"
date: 2023-08-28 18:00:00 +0900
categories: likelion blog
published: true
---

# 테킷 멋쟁이사자처럼 React

- [테킷 멋쟁이사자처럼 React](#테킷-멋쟁이사자처럼-react)
  - [framer Motion](#framer-motion)
  - [적용](#적용)

## framer Motion

AnimatedOutlet.jsx

```jsx
import { useState } from "react";
import { useOutlet } from "react-router-dom";

function AnimatedOutlet() {
  const o = useOutlet();
  const [outlet] = useState(o);
  return <>{outlet}</>;
}

export default AnimatedOutlet;
```

RootLayout.jsx

```jsx
import AnimatedOutlet from '@/components/AnimateOutlet';
import AnimateTransition from '@/components/AnimateTransition';

function RootLayout({ displaySideMenu = false }) {
  return (
    <HeaderBar />
      <main className="flex gap-4 p-5 flex-1 dark:bg-black">
        <AnimateTransition>
          <AnimatedOutlet />
        </AnimateTransition>
      </main>
    <FooterBar />
  );
}
```

AnimateTransition.jsx

```jsx
import { motion, AnimatePresence } from "framer-motion";
import { useLocation } from "react-router-dom";

function AnimateTransition({ children }) {
  const location = useLocation();
  return (
    <AnimatePresence mode="wait">
      <motion.div
        key={location.pathname}
        initial={{ opacity: 0, y: -20 }}
        animate={{ opacity: 1, y: 0 }}
        exit={{ opacity: 0, y: 20 }}
        transition={{
          duration: 0.4,
        }}
        className="h-full"
      >
        {children}
      </motion.div>
    </AnimatePresence>
  );
}
```

## 적용

Heading.jsx

```jsx
import { Link } from "react-router-dom";
import { motion } from "framer-motion";
import { FramerLogo } from "./Logo";
import { useState } from "react";

function Heading() {
  const [animKey, setAnimKey] = useState(0);

  const handleRefreshAnimation = () => {
    setAnimKey((key) => (key += 1));
  };

  return (
    <motion.h1 whileHover={{ scale: 1.2, rotate: -2 }}>
      <Link to="/" onClick={handleRefreshAnimation}>
        <FramerLogo key={animKey} size={60} className="text-blue-300" />
      </Link>
    </motion.h1>
  );
}

export default Heading;
```

Footer.jsx

```jsx
import { useState } from "react";
import { motion } from "framer-motion";

function FooterBar() {
  const [currentYear] = useState(() => new Date().getFullYear());

  return (
    <footer className="p-5 grid place-content-center bg-black dark:border-t dark:border-t-zinc-50/20">
      <motion.small
        drag="x"
        dragConstraints={{
          left: -2,
          right: 2,
        }}
        className="text-base text-sky-500/90 hover:text-sky-500"
      >
        Copyright 2020-{currentYear} &copy; <strong>EUID</strong>
      </motion.small>
    </footer>
  );
}

export default FooterBar;
```

Products.jsx

```jsx
const list = {
  hidden: { opacity: 0 },
  visible: {
    opacity: 1,
    transition: {
      duration: 0.6,
      delayChildren: 0,
      staggerChildren: 0.3,
    },
  },
};

const listItem = {
  hidden: { y: 20, opacity: 0 },
  visible: {
    y: 0,
    opacity: 1,
    transition: {
      y: { duration: 0.4 },
    },
  },
};

<motion.ul
  className="grid gap-10 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4"
  variants={list}
  initial="hidden"
  animate="visible"
>
  {data.items.map((item) => (
    <ProductItem key={item.id} item={item} />
  ))}
</motion.ul>;
```

Logo.jsx

```jsx
export function FramerLogo({ size = 60 }) {
  return (
    <svg width={size} height={size} viewBox="0 0 32 32" fill="none">
      <motion.path
        fillRule="evenodd"
        clipRule="evenodd"
        d="M9 20.3335H16V27.0002L9 20.3335Z"
        stroke-linecap="round"
        stroke-linejoin="round"
        className="stroke-blue-400 stroke-[0.3px]"
        variants={createIconVariants('#0055FF')}
        initial="hidden"
        animate="visible"
        {% raw %}
        transition={{
          default: { duration: 0.75, ease: 'easeInOut' },
          fill: { duration: 1, ease: [1, 0, 0.8, 1] },
        }}
        {% endraw %}
      />
      <motion.path
        d="M16 13.6665H9V20.3332H23L16 13.6665Z"
        stroke-linecap="round"
        stroke-linejoin="round"
        className="stroke-blue-400 stroke-[0.3px]"
        variants={createIconVariants('#00AAFF')}
        initial="hidden"
        animate="visible"
        {% raw %}
        transition={{
          default: { duration: 0.75, delay: 0.25, ease: 'easeInOut' },
          fill: { duration: 1, delay: 0.25, ease: [1, 0, 0.8, 1] },
        }}
        {% endraw %}
      />
      <motion.path
        d="M9 7L16 13.6667H23V7H9Z"
        stroke-linecap="round"
        stroke-linejoin="round"
        className="stroke-blue-400 stroke-[0.3px]"
        variants={createIconVariants('#88DDFF')}
        initial="hidden"
        animate="visible"
        {% raw %}
        transition={{
          default: { duration: 0.75, delay: 0.5, ease: 'easeInOut' },
          fill: { duration: 1, delay: 0.5, ease: [1, 0, 0.8, 1] },
        }}
        {% endraw %}
      />
    </svg>
  );
}
```
