[index.html](https://github.com/user-attachments/files/25838885/index.html)
<!DOCTYPE html>

<html lang="ru">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Расписание клиники</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react/18.2.0/umd/react.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react-dom/18.2.0/umd/react-dom.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/7.23.2/babel.min.js"></script>
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  body{font-family:'Segoe UI',system-ui,sans-serif;background:#f0f4f8;min-height:100vh}
  ::-webkit-scrollbar{height:5px;width:5px}
  ::-webkit-scrollbar-track{background:#f1f5f9}
  ::-webkit-scrollbar-thumb{background:#cbd5e1;border-radius:3px}
  @keyframes slideUp{from{transform:translateY(16px);opacity:0}to{transform:translateY(0);opacity:1}}
  @keyframes fadeIn{from{opacity:0}to{opacity:1}}
  @keyframes pulse{0%,100%{opacity:1}50%{opacity:.6}}
  table{border-collapse:collapse}
</style>
</head>
<body>
<div id="root"></div>
<script type="text/babel">
const {useState,useEffect,useRef,useCallback}=React;

// ══════════════════════════════════════════════════════
// ПОЛЬЗОВАТЕЛИ И РОЛИ
// Чтобы добавить нового — добавьте строку в этот список
// ══════════════════════════════════════════════════════
const USERS = [
{ login:“admin”,    password:“clinic2026”,  name:“Администратор”,   role:“admin”,    avatar:“👩‍💼”, color:”#7c3aed” },
{ login:“doctor”,   password:“doctor2026”,  name:“Ортопед”,         role:“doctor”,   avatar:“🦴”,  color:”#2563eb” },
{ login:“massage”,  password:“massage2026”, name:“Массажист”,        role:“massage”,  avatar:“💆”, color:”#059669” },
{ login:“director”, password:“director26”,  name:“Руководитель”,    role:“director”, avatar:“👔”, color:”#dc2626” },
];

// Права по ролям
const PERMISSIONS = {
admin:    { canBook:true,  canDelete:true,  canChangeStatus:true,  canSeeAll:true,  canSeeStats:true,  label:“Администратор” },
doctor:   { canBook:true,  canDelete:false, canChangeStatus:true,  canSeeAll:true,  canSeeStats:false, label:“Врач” },
massage:  { canBook:true,  canDelete:false, canChangeStatus:true,  canSeeAll:false, canSeeStats:false, label:“Массажист” },
director: { canBook:false, canDelete:true,  canChangeStatus:false, canSeeAll:true,  canSeeStats:true,  label:“Руководитель” },
};

// ══════════════════════════════════════════════════════
// ДАННЫЕ РАСПИСАНИЯ (из вашего Excel)
// ══════════════════════════════════════════════════════
const RAW_DATA = {
“Ортопед”:[
{dates:[“05.01.2026”,“06.01.2026”,“07.01.2026”,“08.01.2026”,“09.01.2026”,“10.01.2026”,“11.01.2026”],
slots:[
{time:“08:30”,day:5,patient:“Соломеин Артем”,status:“booked”},
{time:“09:00”,day:0,patient:“Поддубный”,status:“booked”},
{time:“09:00”,day:1,patient:“Растопчина”,status:“booked”},
{time:“09:00”,day:2,patient:“Бандин с женой”,status:“booked”},
{time:“09:00”,day:3,patient:“Бандин с женой”,status:“booked”},
{time:“09:30”,day:3,patient:“Москат”,status:“booked”},
{time:“09:30”,day:5,patient:“Соломеина Ангелина”,status:“booked”},
{time:“10:00”,day:0,patient:“Лободинский”,status:“booked”},
{time:“10:00”,day:1,patient:“Черноскутова”,status:“booked”},
{time:“10:00”,day:2,patient:“Мануев”,status:“booked”},
{time:“10:00”,day:3,patient:“Прохоренко”,status:“cancel”},
{time:“10:00”,day:4,patient:“Силенко на К.Я”,status:“procedure”},
{time:“10:00”,day:5,patient:“Макар”,status:“booked”},
{time:“10:30”,day:2,patient:“Бобков”,status:“booked”},
{time:“10:30”,day:3,patient:“Коженкова”,status:“booked”},
{time:“10:30”,day:5,patient:“Малышев Али”,status:“transfer”},
{time:“11:00”,day:0,patient:“Ишкова”,status:“booked”},
{time:“11:00”,day:1,patient:“Мартынов”,status:“booked”},
{time:“11:00”,day:3,patient:“Бутырин”,status:“booked”},
{time:“11:00”,day:4,patient:“Махатачян А.В”,status:“booked”},
{time:“11:00”,day:5,patient:“Самойленко”,status:“booked”},
{time:“12:00”,day:0,patient:“Хоружая”,status:“booked”},
{time:“12:00”,day:1,patient:“Свечников”,status:“booked”},
{time:“12:00”,day:4,patient:“Троян”,status:“booked”},
{time:“13:00”,day:0,patient:“Фридман”,status:“booked”},
{time:“13:00”,day:1,patient:“Ковалев”,status:“booked”},
{time:“13:00”,day:3,patient:“Зальцман”,status:“booked”},
{time:“13:00”,day:4,patient:“Татьяна база”,status:“procedure”},
{time:“13:00”,day:5,patient:“Семченко”,status:“booked”},
{time:“14:00”,day:0,patient:“Семенова”,status:“booked”},
{time:“14:00”,day:1,patient:“Некрасова”,status:“booked”},
{time:“14:00”,day:5,patient:“Ткаченко”,status:“booked”},
{time:“14:30”,day:3,patient:“Свечников СВФ”,status:“procedure”},
{time:“14:30”,day:5,patient:“Бондарев”,status:“booked”},
{time:“16:00”,day:3,patient:“Кувтыченко”,status:“booked”},
]},
{dates:[“12.01.2026”,“13.01.2026”,“14.01.2026”,“15.01.2026”,“16.01.2026”,“17.01.2026”,“18.01.2026”],
slots:[
{time:“09:00”,day:1,patient:“Коженкова”,status:“booked”},
{time:“09:00”,day:4,patient:“Антропова”,status:“booked”},
{time:“09:00”,day:5,patient:“Трунин К.Я”,status:“procedure”},
{time:“10:00”,day:5,patient:“Ермаков гиал”,status:“procedure”},
{time:“10:30”,day:1,patient:“Божко”,status:“booked”},
{time:“11:00”,day:0,patient:“Юдина”,status:“booked”},
{time:“11:00”,day:1,patient:“Попова Н.Д перенос”,status:“transfer”},
{time:“11:00”,day:3,patient:“Леваева”,status:“booked”},
{time:“11:00”,day:4,patient:“Ковалев”,status:“booked”},
{time:“11:00”,day:5,patient:“Фролушкин”,status:“booked”},
{time:“12:00”,day:0,patient:“Чугунова”,status:“booked”},
{time:“12:30”,day:2,patient:“Бондарев”,status:“booked”},
{time:“12:30”,day:3,patient:“Ковалев Ю”,status:“booked”},
{time:“12:30”,day:5,patient:“Келишев база”,status:“procedure”},
{time:“13:00”,day:0,patient:“Кузембаева Н.А”,status:“booked”},
{time:“13:00”,day:3,patient:“Шустова — не будет”,status:“cancel”},
{time:“13:00”,day:4,patient:“Солиева”,status:“booked”},
{time:“14:00”,day:3,patient:“Свечников К.Я”,status:“procedure”},
{time:“14:00”,day:5,patient:“Ковалев”,status:“booked”},
{time:“15:00”,day:0,patient:“Аллабердыева”,status:“booked”},
{time:“15:00”,day:5,patient:“Горнак”,status:“booked”},
{time:“17:30”,day:0,patient:“Егорова СВФ”,status:“procedure”},
{time:“17:00”,day:1,patient:“Выгонная К.Я”,status:“procedure”},
]},
{dates:[“19.01.2026”,“20.01.2026”,“21.01.2026”,“22.01.2026”,“23.01.2026”,“24.01.2026”,“25.01.2026”],
slots:[
{time:“08:00”,day:0,patient:“Егорова”,status:“booked”},
{time:“09:00”,day:0,patient:“Ермаков”,status:“booked”},
{time:“09:00”,day:1,patient:“Ермаков”,status:“booked”},
{time:“09:00”,day:2,patient:“Мельникова”,status:“booked”},
{time:“09:00”,day:4,patient:“Антроповы”,status:“booked”},
{time:“10:00”,day:0,patient:“Сафронов”,status:“booked”},
{time:“10:00”,day:4,patient:“Медведева”,status:“booked”},
{time:“10:30”,day:1,patient:“Юртаева К.Я”,status:“procedure”},
{time:“10:30”,day:2,patient:“Мануев”,status:“booked”},
{time:“10:30”,day:3,patient:“Сивкова”,status:“booked”},
{time:“11:00”,day:1,patient:“Божко к.я.”,status:“procedure”},
{time:“12:00”,day:0,patient:“Таранов П.И”,status:“booked”},
{time:“12:00”,day:2,patient:“Божко”,status:“booked”},
{time:“12:00”,day:3,patient:“Голубцова”,status:“booked”},
]},
{dates:[“26.01.2026”,“27.01.2026”,“28.01.2026”,“29.01.2026”,“30.01.2026”,“31.01.2026”,“01.02.2026”],
slots:[
{time:“09:00”,day:4,patient:“Непомнящая капл.”,status:“procedure”},
{time:“09:00”,day:5,patient:“Лободинский к.я.”,status:“procedure”},
{time:“10:00”,day:1,patient:“Божко”,status:“booked”},
{time:“10:00”,day:4,patient:“Середа перв.”,status:“booked”},
{time:“10:00”,day:5,patient:“Синицыны”,status:“booked”},
{time:“11:00”,day:0,patient:“Клюшникова — отмена”,status:“cancel”},
{time:“11:00”,day:1,patient:“Борисова с рентгеном”,status:“booked”},
{time:“11:00”,day:4,patient:“Гасанов перенос”,status:“transfer”},
{time:“11:00”,day:5,patient:“Батрин Евгений”,status:“booked”},
{time:“12:00”,day:0,patient:“Непомнящая”,status:“booked”},
{time:“12:00”,day:1,patient:“Качанова — отмена”,status:“cancel”},
{time:“12:00”,day:2,patient:“Кондратенко”,status:“booked”},
{time:“12:00”,day:4,patient:“Попова Н.Д укол”,status:“procedure”},
{time:“13:00”,day:0,patient:“Сафронов”,status:“booked”},
{time:“13:00”,day:4,patient:“Голубцова укол”,status:“procedure”},
{time:“13:00”,day:5,patient:“Рященцев с женой”,status:“booked”},
{time:“14:00”,day:0,patient:“Юдина перенос”,status:“transfer”},
{time:“14:00”,day:2,patient:“Медведева СВФ”,status:“procedure”},
{time:“16:00”,day:2,patient:“Юдина к.я.”,status:“procedure”},
]},
],
“Массажист”:[
{dates:[””,””,””,””,“06.02.2026”,””,””],
slots:[
{time:“10:00”,day:4,patient:“Непомнящая”,status:“booked”},
{time:“11:00”,day:4,patient:“Канцелярская”,status:“booked”},
{time:“12:00”,day:4,patient:“Попова”,status:“booked”},
]},
{dates:[“09.02.2026”,“10.02.2026”,“11.02.2026”,“12.02.2026”,“13.02.2026”,“14.02.2026”,“15.02.2026”],
slots:[
{time:“10:00”,day:0,patient:“Канцелярская”,status:“booked”},
{time:“10:00”,day:2,patient:“Канцелярская”,status:“booked”},
{time:“10:00”,day:3,patient:“Соломеина”,status:“booked”},
{time:“10:00”,day:4,patient:“Канцелярская”,status:“booked”},
{time:“11:00”,day:0,patient:“Соломеина”,status:“booked”},
{time:“11:00”,day:3,patient:“Канцелярская”,status:“booked”},
{time:“12:00”,day:0,patient:“Попова”,status:“booked”},
{time:“12:00”,day:2,patient:“Попова Наталья”,status:“booked”},
{time:“12:00”,day:3,patient:“Попова”,status:“booked”},
{time:“12:00”,day:4,patient:“Попова”,status:“booked”},
{time:“14:00”,day:4,patient:“Соломеина”,status:“booked”},
]},
]
};

const TIMES=[“08:00”,“08:30”,“09:00”,“09:30”,“10:00”,“10:30”,“11:00”,“11:30”,“12:00”,“12:30”,“13:00”,“13:30”,“14:00”,“14:30”,“15:00”,“15:30”,“16:00”,“16:30”,“17:00”,“17:30”,“18:00”];
const DAYS=[“Пн”,“Вт”,“Ср”,“Чт”,“Пт”,“Сб”,“Вс”];
const DAYS_FULL=[“Понедельник”,“Вторник”,“Среда”,“Четверг”,“Пятница”,“Суббота”,“Воскресенье”];
const PROC_TYPES=[“Первичный приём”,“Повторный приём”,“PRP-терапия”,“СВФ”,“К.Я (капельница)”,“УЗИ сустава”,“УВТ”,“Подология”,“Массаж”,“ЛФК”];
const STATUS_CFG={
booked:   {label:“Записан”,  color:”#065f46”,bg:”#d1fae5”,border:”#6ee7b7”,dot:”#059669”},
procedure:{label:“Процедура”,color:”#1e40af”,bg:”#dbeafe”,border:”#93c5fd”,dot:”#2563eb”},
transfer: {label:“Перенос”, color:”#92400e”,bg:”#fef3c7”,border:”#fcd34d”,dot:”#d97706”},
cancel:   {label:“Отмена”,  color:”#991b1b”,bg:”#fee2e2”,border:”#fca5a5”,dot:”#ef4444”},
};

function buildState(raw){
const s={};
for(const[sp,weeks]of Object.entries(raw)){
s[sp]=weeks.map(w=>{
const grid={};
for(const sl of w.slots){
const k=`${sl.time}_${sl.day}`;
if(!grid[k])grid[k]=[];
grid[k].push({patient:sl.patient,status:sl.status,id:Math.random().toString(36).slice(2)});
}
return{dates:w.dates,grid};
});
}
return s;
}

// ══════════════════════════════════════════════════════
// ЭКРАН ВХОДА
// ══════════════════════════════════════════════════════
function LoginScreen({onLogin}){
const[login,setLogin]=useState(””);
const[pass,setPass]=useState(””);
const[err,setErr]=useState(””);
const[showPass,setShowPass]=useState(false);
const passRef=useRef();

const handle=()=>{
const u=USERS.find(u=>u.login===login.trim()&&u.password===pass);
if(u){onLogin(u);}
else{setErr(“Неверный логин или пароль”);setTimeout(()=>setErr(””),2500);}
};

return(
<div style={{minHeight:“100vh”,background:“linear-gradient(135deg,#0f172a 0%,#1e3a5f 50%,#1e40af 100%)”,display:“flex”,alignItems:“center”,justifyContent:“center”,padding:20}}>
<div style={{background:“rgba(255,255,255,0.97)”,borderRadius:24,padding:40,width:420,maxWidth:“100%”,boxShadow:“0 30px 80px rgba(0,0,0,0.4)”,animation:“slideUp 0.3s ease”}}>
<div style={{textAlign:“center”,marginBottom:32}}>
<div style={{fontSize:48,marginBottom:8}}>🏥</div>
<div style={{fontSize:22,fontWeight:800,color:”#0f172a”,letterSpacing:-0.5}}>Клиника</div>
<div style={{fontSize:13,color:”#64748b”,marginTop:4}}>Система управления расписанием</div>
</div>

```
    <div style={{marginBottom:16}}>
      <label style={{fontSize:12,fontWeight:700,color:"#374151",textTransform:"uppercase",letterSpacing:0.5,display:"block",marginBottom:6}}>Логин</label>
      <input value={login} onChange={e=>setLogin(e.target.value)}
        onKeyDown={e=>e.key==="Enter"&&passRef.current?.focus()}
        placeholder="Введите логин"
        style={{width:"100%",padding:"12px 16px",border:"2px solid #e5e7eb",borderRadius:12,fontSize:14,outline:"none",transition:"border 0.15s",fontFamily:"inherit"}}
        onFocus={e=>e.target.style.border="2px solid #3b82f6"}
        onBlur={e=>e.target.style.border="2px solid #e5e7eb"}/>
    </div>

    <div style={{marginBottom:20,position:"relative"}}>
      <label style={{fontSize:12,fontWeight:700,color:"#374151",textTransform:"uppercase",letterSpacing:0.5,display:"block",marginBottom:6}}>Пароль</label>
      <input ref={passRef} type={showPass?"text":"password"} value={pass}
        onChange={e=>setPass(e.target.value)}
        onKeyDown={e=>e.key==="Enter"&&handle()}
        placeholder="Введите пароль"
        style={{width:"100%",padding:"12px 48px 12px 16px",border:"2px solid #e5e7eb",borderRadius:12,fontSize:14,outline:"none",transition:"border 0.15s",fontFamily:"inherit"}}
        onFocus={e=>e.target.style.border="2px solid #3b82f6"}
        onBlur={e=>e.target.style.border="2px solid #e5e7eb"}/>
      <button onClick={()=>setShowPass(!showPass)}
        style={{position:"absolute",right:14,top:34,background:"none",border:"none",cursor:"pointer",fontSize:18,color:"#94a3b8"}}>
        {showPass?"🙈":"👁"}
      </button>
    </div>

    {err&&<div style={{background:"#fee2e2",color:"#dc2626",padding:"10px 14px",borderRadius:10,fontSize:13,marginBottom:16,textAlign:"center",fontWeight:600}}>⚠️ {err}</div>}

    <button onClick={handle}
      style={{width:"100%",padding:"14px",border:"none",borderRadius:12,background:"linear-gradient(135deg,#1e3a5f,#2563eb)",color:"#fff",fontSize:15,fontWeight:700,cursor:"pointer",boxShadow:"0 4px 15px rgba(37,99,235,0.4)",transition:"transform 0.1s"}}
      onMouseDown={e=>e.currentTarget.style.transform="scale(0.98)"}
      onMouseUp={e=>e.currentTarget.style.transform="scale(1)"}>
      Войти →
    </button>

    <div style={{marginTop:24,padding:16,background:"#f8fafc",borderRadius:12,border:"1px solid #e2e8f0"}}>
      <div style={{fontSize:11,fontWeight:700,color:"#64748b",marginBottom:10,textTransform:"uppercase",letterSpacing:0.5}}>Доступные роли:</div>
      <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:6}}>
        {USERS.map(u=>(
          <button key={u.login} onClick={()=>{setLogin(u.login);setPass(u.password);}}
            style={{padding:"7px 10px",borderRadius:8,border:"1px solid #e2e8f0",background:"#fff",cursor:"pointer",textAlign:"left",fontSize:12,display:"flex",alignItems:"center",gap:6,transition:"all 0.1s"}}
            onMouseEnter={e=>{e.currentTarget.style.borderColor=u.color;e.currentTarget.style.background="#f8fafc"}}
            onMouseLeave={e=>{e.currentTarget.style.borderColor="#e2e8f0";e.currentTarget.style.background="#fff"}}>
            <span style={{fontSize:16}}>{u.avatar}</span>
            <div>
              <div style={{fontWeight:700,color:"#0f172a"}}>{u.name}</div>
              <div style={{color:"#94a3b8",fontSize:10}}>{PERMISSIONS[u.role].label}</div>
            </div>
          </button>
        ))}
      </div>
    </div>
  </div>
</div>
```

);
}

// ══════════════════════════════════════════════════════
// МОДАЛ ЗАПИСИ
// ══════════════════════════════════════════════════════
function BookingModal({slot,onClose,onSave,perms}){
const[name,setName]=useState(””);
const[phone,setPhone]=useState(””);
const[proc,setProc]=useState(PROC_TYPES[0]);
const[note,setNote]=useState(””);
const ref=useRef();
useEffect(()=>ref.current?.focus(),[]);

const save=()=>{
if(!name.trim())return;
const status=[“PRP”,“СВФ”,“К.Я”,“УЗИ”,“УВТ”].some(p=>proc.includes(p))?“procedure”:“booked”;
onSave({patient:name.trim(),phone,procedure:proc,note,status});
};

return(
<div style={{position:“fixed”,inset:0,background:“rgba(15,23,42,0.75)”,backdropFilter:“blur(5px)”,zIndex:1000,display:“flex”,alignItems:“center”,justifyContent:“center”,padding:16}}
onClick={e=>e.target===e.currentTarget&&onClose()}>
<div style={{background:”#fff”,borderRadius:20,padding:32,width:440,maxWidth:“100%”,boxShadow:“0 30px 80px rgba(0,0,0,0.3)”,animation:“slideUp 0.2s ease”}}>
<div style={{display:“flex”,justifyContent:“space-between”,alignItems:“flex-start”,marginBottom:22}}>
<div>
<div style={{fontSize:18,fontWeight:800,color:”#0f172a”}}>📋 Новая запись</div>
<div style={{fontSize:13,color:”#64748b”,marginTop:3}}>
{slot.dayFull} · <b style={{color:”#2563eb”}}>{slot.time}</b> · {slot.date||””}
</div>
</div>
<button onClick={onClose} style={{border:“none”,background:”#f1f5f9”,borderRadius:10,width:34,height:34,cursor:“pointer”,fontSize:18,color:”#94a3b8”}}>×</button>
</div>
{[
[“ФИО пациента *”,“text”,name,setName,“Иванов Иван Иванович”,ref],
[“Телефон”,“tel”,phone,setPhone,”+7 (__*) ***-**-**”,null],
].map(([label,type,val,set,ph,r])=>(
<div key={label} style={{marginBottom:14}}>
<label style={{fontSize:12,fontWeight:700,color:”#374151”,textTransform:“uppercase”,letterSpacing:0.4,display:“block”,marginBottom:5}}>{label}</label>
<input ref={r} type={type} value={val} onChange={e=>set(e.target.value)}
onKeyDown={e=>e.key===“Enter”&&save()}
placeholder={ph}
style={{width:“100%”,padding:“10px 14px”,border:“2px solid #e5e7eb”,borderRadius:10,fontSize:14,outline:“none”,fontFamily:“inherit”}}
onFocus={e=>e.target.style.border=“2px solid #3b82f6”}
onBlur={e=>e.target.style.border=“2px solid #e5e7eb”}/>
</div>
))}
<div style={{marginBottom:14}}>
<label style={{fontSize:12,fontWeight:700,color:”#374151”,textTransform:“uppercase”,letterSpacing:0.4,display:“block”,marginBottom:5}}>Процедура / тип визита</label>
<select value={proc} onChange={e=>setProc(e.target.value)}
style={{width:“100%”,padding:“10px 14px”,border:“2px solid #e5e7eb”,borderRadius:10,fontSize:14,outline:“none”,background:”#fff”,fontFamily:“inherit”}}>
{PROC_TYPES.map(p=><option key={p}>{p}</option>)}
</select>
</div>
<div style={{marginBottom:22}}>
<label style={{fontSize:12,fontWeight:700,color:”#374151”,textTransform:“uppercase”,letterSpacing:0.4,display:“block”,marginBottom:5}}>Примечание</label>
<textarea value={note} onChange={e=>setNote(e.target.value)} rows={2} placeholder=“Жалобы, источник, особые пометки…”
style={{width:“100%”,padding:“10px 14px”,border:“2px solid #e5e7eb”,borderRadius:10,fontSize:14,outline:“none”,resize:“vertical”,fontFamily:“inherit”}}
onFocus={e=>e.target.style.border=“2px solid #3b82f6”}
onBlur={e=>e.target.style.border=“2px solid #e5e7eb”}/>
</div>
<div style={{display:“flex”,gap:10}}>
<button onClick={onClose} style={{flex:1,padding:“11px”,border:“2px solid #e5e7eb”,borderRadius:10,background:”#fff”,color:”#374151”,fontSize:14,fontWeight:600,cursor:“pointer”}}>Отмена</button>
<button onClick={save} disabled={!name.trim()}
style={{flex:2,padding:“11px”,border:“none”,borderRadius:10,
background:name.trim()?“linear-gradient(135deg,#1e3a5f,#2563eb)”:”#e5e7eb”,
color:name.trim()?”#fff”:”#94a3b8”,fontSize:14,fontWeight:700,cursor:name.trim()?“pointer”:“not-allowed”}}>
✓ Записать пациента
</button>
</div>
</div>
</div>
);
}

// ══════════════════════════════════════════════════════
// КАРТОЧКА ПАЦИЕНТА
// ══════════════════════════════════════════════════════
function PatientCard({entry,onStatusChange,onDelete,perms}){
const[open,setOpen]=useState(false);
const cfg=STATUS_CFG[entry.status];
return(
<div style={{background:cfg.bg,border:`1.5px solid ${cfg.border}`,borderRadius:8,padding:“5px 8px”,marginBottom:3,cursor:“pointer”,transition:“all 0.12s”}}
onClick={()=>setOpen(!open)}>
<div style={{display:“flex”,alignItems:“center”,gap:5}}>
<div style={{width:7,height:7,borderRadius:“50%”,background:cfg.dot,flexShrink:0}}/>
<span style={{fontSize:11.5,fontWeight:600,color:”#0f172a”,flex:1,lineHeight:1.3}}>{entry.patient}</span>
{entry.procedure&&<span style={{fontSize:9,background:“rgba(0,0,0,0.08)”,borderRadius:4,padding:“1px 5px”,color:”#374151”,flexShrink:0,whiteSpace:“nowrap”}}>{entry.procedure.split(” “)[0]}</span>}
</div>
{entry.phone&&!open&&<div style={{fontSize:10,color:”#64748b”,marginTop:2,paddingLeft:12}}>📞 {entry.phone}</div>}
{open&&(
<div style={{marginTop:8,paddingTop:8,borderTop:`1px solid ${cfg.border}`}}>
{entry.phone&&<div style={{fontSize:10,color:”#374151”,marginBottom:3}}>📞 {entry.phone}</div>}
{entry.procedure&&<div style={{fontSize:10,color:”#374151”,marginBottom:3}}>🩺 {entry.procedure}</div>}
{entry.note&&<div style={{fontSize:10,color:”#374151”,marginBottom:6}}>💬 {entry.note}</div>}
<div style={{display:“flex”,flexWrap:“wrap”,gap:4}}>
{perms.canChangeStatus&&Object.entries(STATUS_CFG).filter(([k])=>k!==entry.status).map(([k,v])=>(
<button key={k} onClick={e=>{e.stopPropagation();onStatusChange(k)}}
style={{fontSize:9,padding:“2px 7px”,borderRadius:5,border:`1px solid ${v.border}`,background:v.bg,color:v.color,cursor:“pointer”,fontWeight:600}}>
→ {v.label}
</button>
))}
{perms.canDelete&&(
<button onClick={e=>{e.stopPropagation();if(confirm(`Удалить запись: ${entry.patient}?`))onDelete()}}
style={{fontSize:9,padding:“2px 7px”,borderRadius:5,border:“1px solid #fca5a5”,background:”#fee2e2”,color:”#dc2626”,cursor:“pointer”,fontWeight:600,marginLeft:“auto”}}>
🗑 Удалить
</button>
)}
</div>
</div>
)}
</div>
);
}

// ══════════════════════════════════════════════════════
// ЯЧЕЙКА РАСПИСАНИЯ
// ══════════════════════════════════════════════════════
function Cell({entries=[],onBook,onStatusChange,onDelete,isWeekend,perms}){
const[hover,setHover]=useState(false);
return(
<td style={{padding:“3px 4px”,verticalAlign:“top”,minWidth:130,maxWidth:165,
borderRight:“1px solid #e2e8f0”,borderBottom:“1px solid #e2e8f0”,
background:isWeekend===6?”#fff8f8”:isWeekend===5?”#f8faff”:“transparent”,
transition:“background 0.1s”}}
onMouseEnter={()=>setHover(true)} onMouseLeave={()=>setHover(false)}>
{entries.map((e,i)=>(
<PatientCard key={e.id} entry={e} perms={perms}
onStatusChange={s=>onStatusChange(i,s)}
onDelete={()=>onDelete(i)}/>
))}
{perms.canBook&&(hover||entries.length===0)&&(
<button onClick={onBook}
style={{width:“100%”,padding:“5px 0”,
border:`1.5px dashed ${hover&&entries.length===0?"#3b82f6":"#d1d5db"}`,
borderRadius:7,background:hover&&entries.length===0?”#eff6ff”:“transparent”,
color:hover&&entries.length===0?”#2563eb”:”#94a3b8”,
fontSize:11,fontWeight:600,cursor:“pointer”,marginTop:entries.length?3:0,transition:“all 0.12s”}}>
+ Записать
</button>
)}
</td>
);
}

// ══════════════════════════════════════════════════════
// ПАНЕЛЬ СТАТИСТИКИ
// ══════════════════════════════════════════════════════
function Stats({data}){
const all=Object.values(data).flat().flatMap(w=>Object.values(w.grid).flat());
const c={booked:0,procedure:0,transfer:0,cancel:0};
all.forEach(s=>{if(c[s.status]!==undefined)c[s.status]++;});
return(
<div style={{display:“grid”,gridTemplateColumns:“repeat(auto-fit,minmax(130px,1fr))”,gap:12,marginBottom:20}}>
{[
[“📅”,“Всего записей”,all.length,”#1e40af”,”#dbeafe”],
[“✅”,“Записаны”,c.booked,”#065f46”,”#d1fae5”],
[“💉”,“Процедуры”,c.procedure,”#1e40af”,”#dbeafe”],
[“🔄”,“Переносы”,c.transfer,”#92400e”,”#fef3c7”],
[“❌”,“Отмены”,c.cancel,”#991b1b”,”#fee2e2”],
].map(([ic,label,val,color,bg])=>(
<div key={label} style={{background:bg,borderRadius:12,padding:“12px 16px”,border:`1px solid ${color}22`}}>
<div style={{fontSize:20,marginBottom:4}}>{ic}</div>
<div style={{fontSize:22,fontWeight:800,color}}>{val}</div>
<div style={{fontSize:12,color:”#64748b”}}>{label}</div>
</div>
))}
</div>
);
}

// ══════════════════════════════════════════════════════
// ГЛАВНОЕ ПРИЛОЖЕНИЕ
// ══════════════════════════════════════════════════════
function App(){
const[user,setUser]=useState(null);
const[data,setData]=useState(()=>buildState(RAW_DATA));
const[spec,setSpec]=useState(“Ортопед”);
const[weekIdx,setWeekIdx]=useState(0);
const[modal,setModal]=useState(null);
const[search,setSearch]=useState(””);
const[toast,setToast]=useState(null);
const[tab,setTab]=useState(“schedule”); // schedule | stats | users

const perms=user?PERMISSIONS[user.role]:{};
const visibleSpecs=user?.role===“massage”?[“Массажист”]:Object.keys(data);
const weeks=data[spec]||[];
const week=weeks[weekIdx]||{dates:[],grid:{}};

const allTimes=TIMES.filter(t=>Object.keys(week.grid).some(k=>k.startsWith(t)));
const displayTimes=allTimes.length?allTimes:TIMES.slice(2,14);

const toast_=useCallback((msg,color=”#059669”)=>{
setToast({msg,color});setTimeout(()=>setToast(null),2500);
},[]);

const handleSave=({patient,phone,procedure,note,status})=>{
const k=`${modal.time}_${modal.day}`;
setData(prev=>{
const n=JSON.parse(JSON.stringify(prev));
if(!n[spec][weekIdx].grid[k])n[spec][weekIdx].grid[k]=[];
n[spec][weekIdx].grid[k].push({patient,phone,procedure,note,status,id:Math.random().toString(36).slice(2)});
return n;
});
setModal(null);
toast_(`✓ ${patient} записан(а) на ${modal.time}`);
};

const handleStatus=(time,day,idx,ns)=>{
const k=`${time}_${day}`;
setData(prev=>{const n=JSON.parse(JSON.stringify(prev));n[spec][weekIdx].grid[k][idx].status=ns;return n;});
toast_(`Статус → ${STATUS_CFG[ns].label}`,STATUS_CFG[ns].dot);
};

const handleDelete=(time,day,idx)=>{
const k=`${time}_${day}`;
setData(prev=>{
const n=JSON.parse(JSON.stringify(prev));
n[spec][weekIdx].grid[k].splice(idx,1);
if(!n[spec][weekIdx].grid[k].length)delete n[spec][weekIdx].grid[k];
return n;
});
toast_(“Запись удалена”,”#ef4444”);
};

const searchResults=search.length>1
?visibleSpecs.flatMap(sp=>data[sp].flatMap((w,wi)=>
Object.entries(w.grid).flatMap(([k,es])=>
es.filter(e=>e.patient.toLowerCase().includes(search.toLowerCase()))
.map(e=>({…e,sp,wi,time:k.split(”*”)[0],day:parseInt(k.split(”*”)[1]),date:w.dates[parseInt(k.split(”_”)[1])]||””}))
)
))
:[];

if(!user)return <LoginScreen onLogin={u=>{setUser(u);const sv=u.role===“massage”?“Массажист”:“Ортопед”;setSpec(sv);}}/>;

return(
<div style={{minHeight:“100vh”,background:”#f0f4f8”}}>
{/* HEADER */}
<div style={{background:“linear-gradient(135deg,#0f172a 0%,#1e3a5f 60%,#1e40af 100%)”,boxShadow:“0 4px 24px rgba(0,0,0,0.3)”}}>
<div style={{padding:“14px 24px”,display:“flex”,alignItems:“center”,justifyContent:“space-between”,flexWrap:“wrap”,gap:12}}>
<div style={{display:“flex”,alignItems:“center”,gap:12}}>
<div style={{fontSize:28}}>🏥</div>
<div>
<div style={{fontSize:18,fontWeight:800,color:”#fff”,letterSpacing:-0.5}}>Расписание клиники</div>
<div style={{fontSize:11,color:“rgba(255,255,255,0.55)”}}>Медицинская информационная система</div>
</div>
</div>
<div style={{display:“flex”,alignItems:“center”,gap:10}}>
{/* Search */}
<div style={{position:“relative”}}>
<input value={search} onChange={e=>setSearch(e.target.value)}
placeholder=“🔍 Поиск пациента…”
style={{padding:“8px 36px 8px 14px”,border:“1.5px solid rgba(255,255,255,0.2)”,borderRadius:9,fontSize:13,
width:200,background:“rgba(255,255,255,0.12)”,color:”#fff”,outline:“none”}}/>
{search&&<button onClick={()=>setSearch(””)} style={{position:“absolute”,right:10,top:“50%”,transform:“translateY(-50%)”,background:“none”,border:“none”,color:“rgba(255,255,255,0.6)”,cursor:“pointer”,fontSize:16}}>×</button>}
</div>
{/* User badge */}
<div style={{display:“flex”,alignItems:“center”,gap:8,background:“rgba(255,255,255,0.12)”,borderRadius:10,padding:“7px 12px”,cursor:“pointer”}}
onClick={()=>setTab(tab===“users”?“schedule”:“users”)}>
<span style={{fontSize:18}}>{user.avatar}</span>
<div>
<div style={{fontSize:12,fontWeight:700,color:”#fff”}}>{user.name}</div>
<div style={{fontSize:10,color:“rgba(255,255,255,0.6)”}}>{PERMISSIONS[user.role].label}</div>
</div>
</div>
<button onClick={()=>setUser(null)}
style={{padding:“7px 14px”,borderRadius:9,border:“1.5px solid rgba(255,255,255,0.25)”,background:“transparent”,color:“rgba(255,255,255,0.7)”,fontSize:12,fontWeight:600,cursor:“pointer”}}>
Выйти
</button>
</div>
</div>

```
    {/* TAB NAV */}
    <div style={{padding:"0 24px",display:"flex",gap:2,borderTop:"1px solid rgba(255,255,255,0.1)"}}>
      {[
        ["schedule","📅 Расписание",true],
        ["stats","📊 Статистика",perms.canSeeStats],
        ["users","👥 Сотрудники",user.role==="director"||user.role==="admin"],
      ].filter(([,, show])=>show).map(([id,label])=>(
        <button key={id} onClick={()=>setTab(id)}
          style={{padding:"10px 18px",border:"none",background:tab===id?"rgba(255,255,255,0.15)":"transparent",
            color:tab===id?"#fff":"rgba(255,255,255,0.5)",fontSize:13,fontWeight:tab===id?700:500,
            cursor:"pointer",borderBottom:tab===id?"2px solid #60a5fa":"2px solid transparent",transition:"all 0.15s"}}>
          {label}
        </button>
      ))}
    </div>

    {/* Specialist tabs (only on schedule) */}
    {tab==="schedule"&&(
      <div style={{padding:"8px 24px",display:"flex",gap:8,borderTop:"1px solid rgba(255,255,255,0.08)"}}>
        {visibleSpecs.map(s=>(
          <button key={s} onClick={()=>{setSpec(s);setWeekIdx(0);}}
            style={{padding:"6px 16px",borderRadius:8,border:"none",cursor:"pointer",fontSize:13,fontWeight:600,transition:"all 0.15s",
              background:spec===s?"#fff":"rgba(255,255,255,0.12)",
              color:spec===s?"#1e3a5f":"rgba(255,255,255,0.85)"}}>
            {s==="Ортопед"?"🦴":s==="Массажист"?"💆":"👤"} {s}
          </button>
        ))}
      </div>
    )}
  </div>

  {/* SEARCH RESULTS */}
  {search.length>1&&(
    <div style={{padding:"14px 24px",background:"#fff",borderBottom:"1px solid #e2e8f0",animation:"fadeIn 0.15s"}}>
      <div style={{fontSize:13,fontWeight:700,color:"#0f172a",marginBottom:10}}>
        Найдено: <span style={{color:"#2563eb"}}>{searchResults.length} записей</span>
      </div>
      <div style={{display:"flex",flexWrap:"wrap",gap:8}}>
        {searchResults.slice(0,15).map((r,i)=>(
          <div key={i} onClick={()=>{setSpec(r.sp);setWeekIdx(r.wi);setSearch("");setTab("schedule");}}
            style={{background:STATUS_CFG[r.status].bg,border:`1.5px solid ${STATUS_CFG[r.status].border}`,
              borderRadius:9,padding:"7px 12px",cursor:"pointer",transition:"transform 0.1s"}}
            onMouseEnter={e=>e.currentTarget.style.transform="scale(1.02)"}
            onMouseLeave={e=>e.currentTarget.style.transform="scale(1)"}>
            <div style={{fontSize:12,fontWeight:700,color:"#0f172a"}}>{r.patient}</div>
            <div style={{fontSize:10,color:"#64748b"}}>{r.sp} · {r.date} {r.time}</div>
          </div>
        ))}
      </div>
    </div>
  )}

  <div style={{padding:"20px 24px"}}>

    {/* STATS TAB */}
    {tab==="stats"&&perms.canSeeStats&&<Stats data={data}/>}

    {/* USERS TAB */}
    {tab==="users"&&(user.role==="director"||user.role==="admin")&&(
      <div style={{animation:"fadeIn 0.2s"}}>
        <div style={{fontSize:18,fontWeight:800,color:"#0f172a",marginBottom:16}}>👥 Доступы сотрудников</div>
        <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fill,minmax(280px,1fr))",gap:14}}>
          {USERS.map(u=>(
            <div key={u.login} style={{background:"#fff",borderRadius:14,padding:20,boxShadow:"0 2px 12px rgba(0,0,0,0.06)",border:`2px solid ${user.login===u.login?"#3b82f6":"#e2e8f0"}`}}>
              <div style={{display:"flex",alignItems:"center",gap:12,marginBottom:14}}>
                <div style={{width:44,height:44,borderRadius:12,background:u.color+"20",display:"flex",alignItems:"center",justifyContent:"center",fontSize:24}}>{u.avatar}</div>
                <div>
                  <div style={{fontWeight:700,color:"#0f172a"}}>{u.name}</div>
                  <div style={{fontSize:12,color:u.color,fontWeight:600}}>{PERMISSIONS[u.role].label}</div>
                </div>
              </div>
              <div style={{fontSize:12,color:"#64748b",marginBottom:10}}>
                <span style={{fontWeight:600,color:"#374151"}}>Логин:</span> <code style={{background:"#f1f5f9",padding:"2px 6px",borderRadius:4}}>{u.login}</code>
              </div>
              <div style={{display:"flex",flexWrap:"wrap",gap:4}}>
                {Object.entries(PERMISSIONS[u.role]).filter(([k])=>k!=="label").map(([k,v])=>(
                  <span key={k} style={{fontSize:10,padding:"3px 8px",borderRadius:6,
                    background:v?"#d1fae5":"#fee2e2",color:v?"#065f46":"#991b1b",fontWeight:600}}>
                    {v?"✓":"✗"} {k.replace("can","").replace(/([A-Z])/g," $1").toLowerCase()}
                  </span>
                ))}
              </div>
            </div>
          ))}
        </div>
        <div style={{marginTop:20,padding:16,background:"#fef3c7",borderRadius:12,border:"1px solid #fcd34d",fontSize:13,color:"#92400e"}}>
          <b>💡 Как изменить пароли:</b> откройте файл clinic-schedule.html в любом текстовом редакторе, найдите блок <code style={{background:"rgba(0,0,0,0.08)",padding:"1px 5px",borderRadius:4}}>USERS</code> в начале скрипта и измените поля <code style={{background:"rgba(0,0,0,0.08)",padding:"1px 5px",borderRadius:4}}>password</code>.
        </div>
      </div>
    )}

    {/* SCHEDULE TAB */}
    {tab==="schedule"&&(
      <>
        {/* Week selector */}
        <div style={{display:"flex",gap:8,marginBottom:16,flexWrap:"wrap",alignItems:"center"}}>
          <span style={{fontSize:13,color:"#64748b",fontWeight:600}}>Неделя:</span>
          {weeks.map((w,i)=>{
            const filled=w.dates.filter(Boolean);
            const label=filled.length?`${filled[0]} — ${filled[filled.length-1]}`:`Неделя ${i+1}`;
            const total=Object.values(w.grid).flat().length;
            return(
              <button key={i} onClick={()=>setWeekIdx(i)}
                style={{padding:"7px 13px",borderRadius:9,border:`2px solid ${weekIdx===i?"#2563eb":"#e2e8f0"}`,
                  background:weekIdx===i?"#2563eb":"#fff",
                  color:weekIdx===i?"#fff":"#374151",fontSize:12,fontWeight:600,cursor:"pointer",transition:"all 0.12s"}}>
                {label}
                <span style={{marginLeft:5,background:weekIdx===i?"rgba(255,255,255,0.25)":"#f1f5f9",borderRadius:5,padding:"1px 6px",fontSize:11}}>{total}</span>
              </button>
            );
          })}
          {perms.canBook&&(
            <button onClick={()=>{
              setData(prev=>{const n=JSON.parse(JSON.stringify(prev));n[spec].push({dates:["","","","","","",""],grid:{}});return n;});
              setWeekIdx(weeks.length);
            }} style={{padding:"7px 13px",borderRadius:9,border:"2px dashed #3b82f6",background:"#eff6ff",color:"#2563eb",fontSize:12,fontWeight:600,cursor:"pointer"}}>
              + Новая неделя
            </button>
          )}
        </div>

        {/* Legend */}
        <div style={{display:"flex",gap:14,marginBottom:14,flexWrap:"wrap"}}>
          {Object.entries(STATUS_CFG).map(([k,v])=>(
            <div key={k} style={{display:"flex",alignItems:"center",gap:5,fontSize:12,color:"#374151"}}>
              <div style={{width:8,height:8,borderRadius:"50%",background:v.dot}}/>
              {v.label}
            </div>
          ))}
        </div>

        {/* TABLE */}
        <div style={{overflowX:"auto",borderRadius:14,boxShadow:"0 2px 20px rgba(0,0,0,0.08)",border:"1px solid #e2e8f0",background:"#fff"}}>
          <table style={{width:"100%",minWidth:920}}>
            <thead>
              <tr style={{background:"linear-gradient(135deg,#0f172a,#1e40af)"}}>
                <th style={{padding:"12px 14px",textAlign:"left",fontSize:11,fontWeight:700,color:"rgba(255,255,255,0.6)",width:72,borderRight:"1px solid rgba(255,255,255,0.1)"}}>ВРЕМЯ</th>
                {DAYS.map((d,i)=>(
                  <th key={i} style={{padding:"12px 10px",textAlign:"center",fontSize:12,fontWeight:700,
                    color:i>=5?"rgba(255,255,255,0.4)":"#fff",borderRight:"1px solid rgba(255,255,255,0.08)",minWidth:130}}>
                    <div>{d}</div>
                    <div style={{fontSize:10,fontWeight:400,opacity:0.7,marginTop:2}}>{week.dates[i]||"—"}</div>
                  </th>
                ))}
              </tr>
            </thead>
            <tbody>
              {displayTimes.map((time,ti)=>(
                <tr key={time} style={{background:ti%2===0?"#fafbfc":"#fff"}}>
                  <td style={{padding:"6px 14px",fontSize:13,fontWeight:700,color:"#2563eb",
                    borderRight:"1px solid #e2e8f0",borderBottom:"1px solid #e2e8f0",
                    verticalAlign:"middle",background:"#f8fafc",whiteSpace:"nowrap"}}>
                    {time}
                  </td>
                  {DAYS.map((_,di)=>{
                    const k=`${time}_${di}`;
                    return(
                      <Cell key={di} entries={week.grid[k]||[]} perms={perms}
                        isWeekend={di===5?5:di===6?6:false}
                        onBook={()=>setModal({time,day:di,date:week.dates[di]||"",dayFull:DAYS_FULL[di]})}
                        onStatusChange={(idx,s)=>handleStatus(time,di,idx,s)}
                        onDelete={idx=>handleDelete(time,di,idx)}/>
                    );
                  })}
                </tr>
              ))}
            </tbody>
          </table>
        </div>

        {/* Quick add */}
        {perms.canBook&&(
          <div style={{marginTop:14,padding:"12px 18px",background:"#fff",borderRadius:12,border:"1.5px dashed #93c5fd",display:"flex",alignItems:"center",gap:10,flexWrap:"wrap"}}>
            <span style={{fontSize:12,color:"#374151",fontWeight:700}}>⚡ Быстрая запись:</span>
            {["08:00","09:00","10:00","11:00","12:00","13:00","14:00","15:00","16:00","17:00"].map(t=>(
              <button key={t} onClick={()=>{
                const fd=DAYS.findIndex((_,di)=>!week.grid[`${t}_${di}`]?.length);
                setModal({time:t,day:fd>=0?fd:0,date:week.dates[fd>=0?fd:0]||"",dayFull:DAYS_FULL[fd>=0?fd:0]});
              }} style={{padding:"5px 12px",borderRadius:7,border:"1px solid #dbeafe",background:"#eff6ff",color:"#2563eb",fontSize:12,fontWeight:600,cursor:"pointer"}}>
                {t}
              </button>
            ))}
          </div>
        )}
      </>
    )}
  </div>

  {modal&&<BookingModal slot={modal} onClose={()=>setModal(null)} onSave={handleSave} perms={perms}/>}

  {toast&&(
    <div style={{position:"fixed",bottom:24,left:"50%",transform:"translateX(-50%)",
      background:toast.color,color:"#fff",padding:"12px 24px",borderRadius:12,
      fontSize:14,fontWeight:700,boxShadow:"0 8px 30px rgba(0,0,0,0.25)",
      animation:"slideUp 0.2s ease",zIndex:2000,whiteSpace:"nowrap"}}>
      {toast.msg}
    </div>
  )}
</div>
```

);
}

ReactDOM.createRoot(document.getElementById(“root”)).render(<App/>);
</script>

</body>
</html>
