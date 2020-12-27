# Introduction to YAML (อ่านว่า ยำมอล์ )
อะไร คือ YAML หรืออ่านว่า ยำมอล์ ?
YAML คือ data format แบบหนึ่งที่นิยม นำมาใช้กำหนดรูปแบบของระบบต่างๆ มากมายในปัจจุบัน ลองเปรียบเทียบรูปแบบตัวอื่นๆ ดูครับ
ตัวอย่าง 

รูปแบบ XML
```
<Servers>
    <Server>
        <name>Server1</name>
        <owner>John</owner>
        <created>12232012</created>
        <status>active</status>
    </Server>
</Servers>
```

รูปแบบ JSON
```
{
    Servers: [
        name: Server1,
        owner: John,
        created: 12232012,
        status: active,
    ]
}
```

รูปแบบ YAML
```
Servers:
  - name: Server1
     owner: John
     created: 12232012
     status: active
```

## YAML format

Key Value Pair
```
Fruit: Apple
Vegetable: Carrot
Liquid: Water
Meat: Chicken
```

Array/Lists
```
Fruits: 
- Orange
- Apple
- Banana

Vegetables:
- Carrot
- Cauliflower
- Tomato
```

Dictionary/Map
```
Banana:
  Calories: 105
  Fat: 0.4 g
  Carbs: 27 g

Grapes:
  Calories: 62
  Fat: 0.3 g
  Carbs: 16 g
```

### YAML Spaces

Dictionary/Map #1 
```
Banana:
  Calories: 105
  Fat: 0.4 g
  Carbs: 27 g
```
Dictionary/Map #2
```
Banana:
  Calories: 105
    Fat: 0.4 g
    Carbs: 27 g
```
ตัว #1 และ #2 มีความแตกต่างตรง ระยะของวรรคข้างหน้า ช่องว่างไม่เท่ากันทำให้ ความหมายของข้อมูลทั้งสองอันไม่เหมือนกันทั้งที่เขียนเหมือนกัน คือ Equal number of spaces 

### YAML - Advanced
YAML สามารถเขียนให้ซับซ้อนได้ ตัวอย่างเช่น Key Value/Dictionary/List ร่วมกัน
```
Fruits: 
    - Banana:  
        Calories: 105
        Fat: 0.4 g
        Carbs: 27 g

    - Grape:
        Calories: 62
        Fat: 0.3 g
        Carbs: 16 g
```

### Dictionary vs List vs List of Dictionaries

ลองดูตัวอย่าง การกำหนด YAML ไฟล์​ คุณสมบัติของรถ

Dictionary of Dictionary
```
Color: Blue
Model:
    Name: Corvette
    Year: 1995
Transmission: Manual
Price: $20,000
```

แล้วถ้า รถมีหลายสีล่ะ 

List of Strings
```
- Blue Corvette
- Grey Corvette
- Red Corvette
- Green Corvette
- Blue Corvette
```

งั้นรถแต่ละสีที่มีคุณสมบัติของรถล่ะ จะเขียนยังไง?
List of Dictionaries
```
-  Color: Blue
    Model:
        Name: Corvette
        Year: 1995
    Transmission: Manual
    Price: $20,000
-  Color: Grey
    Model:
        Name: Corvette
        Year: 1995
    Transmission: Manual
    Price: $20,000
-  Color: Red
    Model:
        Name: Corvette
        Year: 1995
    Transmission: Manual
    Price: $20,000
-  Color: Green
    Model:
        Name: Corvette
        Year: 1995
    Transmission: Manual
    Price: $20,000
-  Color: Blue
    Model:
        Name: Corvette
        Year: 1995
    Transmission: Manual
    Price: $20,000
```

### YAML - Notes
ในการเขียน YAML มีจุดที่ต้องสังเกตอยู่คือ
1. Dictionary จะมีลักษณะแบบ Unordered
2. List จะมีลักษณะแบบ Ordered

ตัวอย่างเช่น
Dictionary/Map #1
```
Banana:
  Calories: 105
  Fat: 0.4 g
  Carbs: 27 g
```
Dictionary/Map #2
```
Banana:
  Calories: 105
  Carbs: 27 g
  Fat: 0.4 g
```
#1 และ #2 จะมีความหมายที่เหมือนกัน ถ้าจะวางคุณสมบัติสลับกันก็ได้ (Unordered)

แต่สำหรับ List จะต่างไป 
ตัวอย่างเข่น
Array/List #1
```
Fruits: 
    - Orange
    - Apple
    - Banana
```
Array/List #2
```
Fruits: 
    - Orange
    - Banan
    - Apple
```
จากข้างต้นสำหรับ List #1 และ #2 จะไม่เท่ากัน หรือมีความหมายที่แตกต่างกันครับ (Ordered) 
สรุปคือ การเรียงลำดับ(order)มีความสำคัญในการกำหนดของ YAML แบบ List แต่แบบ Dictionary ไม่เป็นไร

สุดท้ายสำหรับการ Comments ต่างๆ ให้ใช้ Hash (#) บนการเขียน YAML ไฟล์