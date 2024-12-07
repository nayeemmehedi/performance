### **Deep Copy এবং Shallow Copy-এর মধ্যে পার্থক্য কী?**

জাভাস্ক্রিপ্টে, **কপি করার ক্ষেত্রে** ডেটার স্ট্রাকচার (যেমন: অবজেক্ট এবং অ্যারে) কপি করার ধরন বুঝতে **Shallow Copy** এবং **Deep Copy** গুরুত্বপূর্ণ।  

#### **1. Shallow Copy (পৃষ্ঠস্থ কপি):**  
- **শুধুমাত্র উপরের স্তর কপি করা হয়।**  
- যদি অবজেক্ট বা অ্যারেতে নেস্টেড (অর্থাৎ আরেক অবজেক্ট বা অ্যারে) ডেটা থাকে, তাহলে তা **reference** হিসেবেই থাকে।  
- এর ফলে মূল ডেটা পরিবর্তিত হলে shallow copy-তেও সেই পরিবর্তন প্রতিফলিত হয়।  

#### **2. Deep Copy (গভীর কপি):**  
- **ডেটার পুরো স্ট্রাকচার (নেস্টেড অবজেক্টসহ) কপি করা হয়।**  
- Deep copy তৈরি করার পর মূল ডেটা পরিবর্তিত হলে, কপিতে এর কোনো প্রভাব পড়ে না।  

---

### **তুলনামূলক টেবিল**

| **পার্থক্য**         | **Shallow Copy**                                      | **Deep Copy**                                         |
|-----------------------|-------------------------------------------------------|------------------------------------------------------|
| **নেস্টেড ডেটা**    | Reference রয়ে যায়।                                   | ডেটার সম্পূর্ণ নতুন কপি তৈরি হয়।                   |
| **মূল ডেটার প্রভাব** | মূল ডেটা পরিবর্তিত হলে কপিতেও প্রভাব পড়ে।             | মূল ডেটা পরিবর্তিত হলেও কপিতে কোনো প্রভাব পড়ে না।  |
| **কপি করার ধরন**    | শুধুমাত্র প্রথম লেভেল কপি করা হয়।                     | সমস্ত লেভেল কপি করা হয়।                             |

---

### **Shallow Copy-এর উদাহরণ**
```javascript
const obj1 = {
    name: "Nayeem",
    details: {
        age: 25,
        country: "Bangladesh"
    }
};

// Shallow Copy তৈরি
const shallowCopy = { ...obj1 };

// পরিবর্তন করা
shallowCopy.details.age = 30;

console.log(obj1.details.age); // Output: 30 (মূল অবজেক্টেও পরিবর্তন হয়েছে)
```

#### **কেন এমন হলো?**
- `details` প্রপার্টি হলো একটি নেস্টেড অবজেক্ট। Shallow copy তে `details` এর reference কপি হয়েছে, ডেটা নয়।

---

### **Deep Copy-এর উদাহরণ**
#### **পদ্ধতি ১: JSON ব্যবহার করা**
```javascript
const obj2 = {
    name: "Nayeem",
    details: {
        age: 25,
        country: "Bangladesh"
    }
};

// Deep Copy তৈরি
const deepCopy = JSON.parse(JSON.stringify(obj2));

// পরিবর্তন করা
deepCopy.details.age = 30;

console.log(obj2.details.age); // Output: 25 (মূল অবজেক্ট পরিবর্তিত হয়নি)
```

#### **সীমাবদ্ধতা:**  
- JSON পদ্ধতি কাজ করে না যদি অবজেক্টে **ফাংশন** বা **undefined** থাকে।  

---

#### **পদ্ধতি ২: রিক্রুসিভ ফাংশন দিয়ে Deep Copy**
```javascript
function deepCopy(obj) {
    if (obj === null || typeof obj !== "object") {
        return obj;
    }
    const copy = Array.isArray(obj) ? [] : {};
    for (let key in obj) {
        copy[key] = deepCopy(obj[key]);
    }
    return copy;
}

const obj3 = {
    name: "Nayeem",
    details: {
        age: 25,
        country: "Bangladesh"
    }
};

const deepCopiedObj = deepCopy(obj3);

// পরিবর্তন করা
deepCopiedObj.details.age = 30;

console.log(obj3.details.age); // Output: 25 (মূল ডেটা অক্ষত)
```

---

### **উপসংহার**
- **Shallow Copy:** দ্রুত, কিন্তু নেস্টেড অবজেক্ট থাকলে সাবধান থাকতে হবে।  
- **Deep Copy:** নিরাপদ, তবে computationally ব্যয়বহুল।  

আপনার ডেটার প্রয়োজন অনুযায়ী কপি পদ্ধতি নির্বাচন করুন।  
আরো প্রশ্ন থাকলে জানান! 😊