`React`-এ `memo`, `useMemo`, এবং `useCallback` তিনটি ভিন্ন হুক যা পারফরমেন্স অপটিমাইজেশনের জন্য ব্যবহৃত হয়। এই হুকগুলোর মূল উদ্দেশ্য হলো রেন্ডারিংয়ের সময় কমানো এবং অপ্রয়োজনীয় রেন্ডার থেকে অ্যাপকে সুরক্ষিত রাখা। আসুন, এগুলোর প্রতিটির কাজ এবং ব্যবহার বুঝে নিই বিভিন্ন উদাহরণের মাধ্যমে।

### 1. **`React.memo`** (Component Memoization)
`React.memo` হল একটি **হাইয়ার অর্ডার কম্পোনেন্ট** যা শুধুমাত্র সেই কম্পোনেন্টটি পুনরায় রেন্ডার করে যদি তার props পরিবর্তিত হয়। অন্যথায় এটি পূর্বের রেন্ডার ফলাফল রিইউজ করে, ফলে অপ্রয়োজনীয় রেন্ডারিং কমানো হয়।

#### উদাহরণ:
```javascript
import React, { useState } from 'react';

// Normal component
function Child({ count }) {
  console.log('Child re-rendered');
  return <div>{count}</div>;
}

// Memoized component using React.memo
const MemoizedChild = React.memo(Child);

function Parent() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment Count</button>
      <button onClick={() => setOtherState(otherState + 1)}>Change Other State</button>
      <MemoizedChild count={count} />
    </div>
  );
}

export default Parent;
```

**ব্যাখ্যা**:
- `Child` কম্পোনেন্টে `count` prop রয়েছে। `React.memo` এর মাধ্যমে, কম্পোনেন্টটি শুধুমাত্র যখন `count` পরিবর্তিত হবে, তখনই রেন্ডার হবে।
- যদি `otherState` পরিবর্তন হয়, তবুও `Child` কম্পোনেন্টটি রেন্ডার হবে না কারণ তার prop (`count`) পরিবর্তিত হয়নি।

---

### 2. **`useMemo`** (Memoization of Values)
`useMemo` একটি হুক যা একটি ভ্যালু ক্যাশে করে রাখে এবং শুধুমাত্র নির্দিষ্ট ডিপেন্ডেন্সি পরিবর্তিত হলে এটি পুনরায় গণনা করে। এটি সাধারণত সেগুলোর জন্য ব্যবহার করা হয় যেগুলোর গণনা করা খুব ব্যয়বহুল এবং পরিবর্তন কম হয়।

#### উদাহরণ:
```javascript
import React, { useState, useMemo } from 'react';

function ExpensiveComponent({ num }) {
  const expensiveCalculation = (n) => {
    console.log('Performing expensive calculation...');
    return n * 2;
  };

  // useMemo used to memoize the result of the expensive calculation
  const result = useMemo(() => expensiveCalculation(num), [num]);

  return <div>Calculated Result: {result}</div>;
}

function Parent() {
  const [num, setNum] = useState(0);
  const [otherState, setOtherState] = useState(0);

  return (
    <div>
      <button onClick={() => setNum(num + 1)}>Increment Number</button>
      <button onClick={() => setOtherState(otherState + 1)}>Change Other State</button>
      <ExpensiveComponent num={num} />
    </div>
  );
}

export default Parent;
```

**ব্যাখ্যা**:
- `useMemo` ব্যবহার করে আমরা expensiveCalculation ফাংশনটি কেবলমাত্র যখন `num` পরিবর্তিত হয় তখনই পুনরায় কল করি। অন্যথায় এটি পূর্ববর্তী ফলাফলই ব্যবহার করবে, যা পারফরমেন্স বাঁচায়।
- যদি `otherState` পরিবর্তন হয়, `ExpensiveComponent` রেন্ডার হবে না এবং `expensiveCalculation` ফাংশনটি আবার চলবে না।

---

### 3. **`useCallback`** (Memoization of Functions)
`useCallback` হুকটি `useMemo` এর মতো, তবে এটি একটি ফাংশনকে মেমোরাইজ করে রাখে। এটি সেই সময় কাজে আসে যখন আপনি একটি ফাংশনকে prop হিসেবে অন্যান্য কম্পোনেন্টে পাঠাচ্ছেন এবং চাইছেন যে এটি শুধুমাত্র নির্দিষ্ট ডিপেন্ডেন্সি পরিবর্তিত হলে পুনরায় তৈরি হোক।

#### উদাহরণ:
```javascript
import React, { useState, useCallback } from 'react';

function Button({ onClick }) {
  console.log('Button re-rendered');
  return <button onClick={onClick}>Click me</button>;
}

function Parent() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(0);

  // useCallback used to memoize the function
  const memoizedOnClick = useCallback(() => {
    setCount(count + 1);
  }, [count]);  // The function will only be recreated when `count` changes

  return (
    <div>
      <button onClick={() => setOtherState(otherState + 1)}>Change Other State</button>
      <Button onClick={memoizedOnClick} />
    </div>
  );
}

export default Parent;
```

**ব্যাখ্যা**:
- `useCallback` হুকটি `memoizedOnClick` ফাংশনকে শুধুমাত্র তখনই পুনরায় তৈরি করবে যখন `count` পরিবর্তিত হবে।
- অন্যথায়, `Button` কম্পোনেন্টের জন্য একই ফাংশন ব্যবহার করা হবে, ফলে অপ্রয়োজনীয় রেন্ডারিং কমবে। 
- `Button` কম্পোনেন্টটি শুধুমাত্র তখনই রেন্ডার হবে যদি `memoizedOnClick` ফাংশন পরিবর্তিত হয়, যা শুধুমাত্র `count` পরিবর্তিত হলে হবে।

---

### **মুল পার্থক্য:**

- **`React.memo`**: এটি কম্পোনেন্ট মেমোরাইজ করে, props পরিবর্তিত না হলে কম্পোনেন্টটি পুনরায় রেন্ডার হয় না।
- **`useMemo`**: এটি ভ্যালু মেমোরাইজ করে, শুধুমাত্র নির্দিষ্ট ডিপেন্ডেন্সি পরিবর্তিত হলে সেই ভ্যালু পুনরায় গণনা হয়।
- **`useCallback`**: এটি ফাংশন মেমোরাইজ করে, শুধুমাত্র নির্দিষ্ট ডিপেন্ডেন্সি পরিবর্তিত হলে সেই ফাংশন পুনরায় তৈরি হয়।

এই হুকগুলোর ব্যবহার পারফরমেন্স অপটিমাইজেশন করতে সাহায্য করে, বিশেষত বড় অ্যাপ্লিকেশন বা কম্পোনেন্টের ক্ষেত্রে যেখানে অপ্রয়োজনীয় রেন্ডার কমানো প্রয়োজন।