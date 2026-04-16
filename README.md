# lr-community-calendar

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Lake Roots Community Calendar</title>
<link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>⛵</text></svg>"/>
<link rel="preconnect" href="https://fonts.googleapis.com"/>
<link href="https://fonts.googleapis.com/css2?family=Libre+Franklin:wght@300;400;500;600;700;800&display=swap" rel="stylesheet"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react/18.2.0/umd/react.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react-dom/18.2.0/umd/react-dom.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/7.23.9/babel.min.js"></script>
<style>
*{margin:0;padding:0;box-sizing:border-box}
body{font-family:'Libre Franklin','Helvetica Neue',sans-serif;background:#FCFBE4;-webkit-font-smoothing:antialiased}
button{font-family:inherit;cursor:pointer}
a{color:inherit;text-decoration:none}
@media(max-width:680px){
  .day-cell{min-height:60px!important;padding:2px 2px 3px!important}
  .day-num{font-size:10px!important}
  .ev-pill{font-size:7px!important;padding:1px 2px!important}
  .ev-pill-text{max-width:52px!important}
  .header-title{font-size:18px!important}
  .header-sub{font-size:7.5px!important;letter-spacing:1.5px!important}
  .filter-btn{font-size:8px!important;padding:3px 7px!important;gap:3px!important}
  .nav-btn{padding:4px 8px!important;font-size:11px!important}
  .day-hdr{font-size:8px!important;letter-spacing:1px!important;padding:5px 2px!important}
  .popup{max-width:210px!important;white-space:normal!important;font-size:10px!important}
  .sub-bar{flex-direction:column!important;gap:6px!important;align-items:flex-start!important}
  .legend{gap:8px!important}
  .legend-item span:last-child{font-size:8px!important}
}
</style>
</head>
<body>
<div id="root"></div>
<script type="text/babel">
const{useState,useMemo,useRef,useEffect}=React;

const EV=[
  // HOLIDAYS
  {d:"2026-04-03",t:"Good Friday",c:"hol"},
  {d:"2026-04-05",t:"Easter Sunday",c:"hol"},
  {d:"2026-05-05",t:"Cinco de Mayo",c:"hol"},
  {d:"2026-05-10",t:"Mother's Day",c:"hol"},
  {d:"2026-05-25",t:"Memorial Day",c:"hol"},
  {d:"2026-06-14",t:"Flag Day",c:"hol"},
  {d:"2026-06-19",t:"Juneteenth",c:"hol"},
  {d:"2026-06-21",t:"Father's Day",c:"hol"},
  {d:"2026-07-04",t:"Independence Day",c:"hol"},

  // LAKE ROOTS
  {d:"2026-04-05",t:"LR Open Easter Wknd",c:"lr",x:"Open normal hours all Easter weekend",tm:"All day",u:"https://lakerootscl.com/events/easter"},
  {d:"2026-04-25",t:"LR Embroidery Pop-Up",c:"lr",x:"Custom embroidery w/ Dani's Craftbook — totes & pouches",tm:"11 AM – 2 PM",u:"https://lakerootscl.com/events/y4akziz1pv41hdwn8nxu47zsiwr7n7"},
  {d:"2026-05-08",t:"LR Book Signing",c:"lr",x:"Amanda Schneider — Work for What's Next",tm:"2 – 5 PM",u:"https://lakerootscl.com/events/book-signing-with-amanda-schneider"},
  {d:"2026-05-09",t:"LR Live Music: Lara Bell",c:"lr",x:"Live music, beverages & bites",tm:"12 – 3 PM",u:"https://lakerootscl.com/events/live-music-with-lara-bell-zh585"},
  {d:"2026-05-10",t:"LR Mother's Day Brunch",c:"lr",x:"Special brunch spread — reservations open! Dinner at 4 PM.",tm:"8 AM – 3 PM",u:"https://lakerootscl.com/events/mothers-day-brunch"},

  // THE DOLE
  {d:"2026-04-19",t:"Dole Farmers Market+",c:"dole",x:"Indoor Market — 50+ farmers, food, artisans, live music",tm:"10 AM – 2 PM",u:"https://www.thedole.org/events-list"},
  {d:"2026-04-23",t:"Dole: CLC Jazz Concert",c:"dole",x:"CL Central HS Spring Jazz — Jazz 1, Jazz 2 & Combo · $10",tm:"3 – 9 PM",u:"https://www.thedole.org/events-list/crystal-lake-central-high-school-jazz-concert"},
  {d:"2026-04-24",t:"Dole: 4th Fridays Art",c:"dole",x:"4 curated exhibits, 2 music stages, bar & roving snacks · $10",tm:"6 – 9 PM",u:"https://www.thedole.org/events-list"},
  {d:"2026-04-26",t:"Dole Farmers Market+",c:"dole",x:"Indoor Market — last of winter season",tm:"10 AM – 2 PM",u:"https://www.thedole.org/events-list"},
  {d:"2026-05-01",t:"Dole: John Primer Blues",c:"dole",x:"Grammy-nom'd blues icon · hors d'oeuvres, cocktails, raffle · Benefits Senior Services",tm:"5:30 – 10 PM",u:"https://www.thedole.org/events-list/eklyffpd72ncqtgxs85006ga0kpvio"},
  {d:"2026-05-22",t:"Dole: 4th Fridays Art",c:"dole",x:"4 curated exhibits, 2 music stages, bar & snacks · $10",tm:"6 – 9 PM",u:"https://www.thedole.org/events-list"},
  {d:"2026-06-13",t:"Dole Outdoor Mkt Opens",c:"dole",x:"Weekly Saturdays through Sept · 8 AM–1 PM",tm:"8 AM – 1 PM",u:"https://www.farmersmarketatthedole.org/"},
  {d:"2026-06-26",t:"Dole: 4th Fridays Art",c:"dole",x:"4 curated exhibits, 2 music stages, bar & snacks · $10",tm:"6 – 9 PM",u:"https://www.thedole.org/events-list"},
  {d:"2026-07-03",t:"Lakeside Festival",c:"dole",x:"CL's biggest fest! 14 bands, carnival, food · July 4th weekend",tm:"12 – 11 PM",u:"https://www.thedole.org/the-fest"},
  {d:"2026-07-24",t:"Dole: 4th Fridays Art",c:"dole",x:"4 curated exhibits, 2 music stages, bar & snacks · $10",tm:"6 – 9 PM",u:"https://www.thedole.org/events-list"},

  // D47
  {d:"2026-04-03",t:"D47 No School",c:"sch",x:"Non-Attendance Day",u:"https://www.d47.org/families/calendar/school-year-calendar"},
  {d:"2026-04-22",t:"D47 Early Release",c:"sch",x:"Grades K-8 · Non-Attendance PreK",u:"https://www.d47.org/families/calendar/school-year-calendar"},
  {d:"2026-05-13",t:"D47 Early Release",c:"sch",x:"Grades K-8 · Non-Attendance PreK",u:"https://www.d47.org/families/calendar/school-year-calendar"},
  {d:"2026-05-20",t:"D47 Last Day PreK & K",c:"sch",x:"Last day for PreK & Kindergarten",u:"https://www.d47.org/families/calendar/school-year-calendar"},
  {d:"2026-05-21",t:"D47 Last Day Gr 1-8",c:"sch",x:"Last day (if no emergency days)",u:"https://www.d47.org/families/calendar/school-year-calendar"},

  // D155
  {d:"2026-04-04",t:"D155 Non-Attendance",c:"sch",x:"Day of Non-Attendance",u:"https://www.d155.org/about/d155-upcoming-events-full-calendar"},
  {d:"2026-04-06",t:"D155 School Resumes",c:"sch",x:"Back from Spring Break",u:"https://www.d155.org/about/d155-upcoming-events-full-calendar"},
  {d:"2026-04-20",t:"D155 Board Meeting",c:"sch",x:"7 PM · 1 S Virginia Rd",u:"https://www.d155.org/about/d155-upcoming-events-full-calendar"},
  {d:"2026-05-18",t:"D155 Board Meeting",c:"sch",x:"7 PM · 1 S Virginia Rd",u:"https://www.d155.org/about/d155-upcoming-events-full-calendar"},
  {d:"2026-05-19",t:"D155 Semester Exams",c:"sch",x:"Exam Day 1",u:"https://www.d155.org/about/d155-upcoming-events-full-calendar"},
  {d:"2026-05-20",t:"D155 Semester Exams",c:"sch",x:"Exam Day 2",u:"https://www.d155.org/about/d155-upcoming-events-full-calendar"},
  {d:"2026-05-25",t:"D155 No School",c:"sch",x:"Memorial Day",u:"https://www.d155.org/about/d155-upcoming-events-full-calendar"},
  {d:"2026-06-15",t:"D155 Board Meeting",c:"sch",x:"7 PM · 1 S Virginia Rd",u:"https://www.d155.org/about/d155-upcoming-events-full-calendar"},

  // LOCAL
  {d:"2026-04-18",t:"Chamber Clean-Up Day",c:"loc",x:"CL Chamber annual community clean-up",u:"https://business.clchamber.com/events"},
  {d:"2026-04-22",t:"Social Dance Night",c:"loc",x:"Crystal Lake Park District",u:"https://www.crystallakeparks.org/special-events"},
  {d:"2026-05-17",t:"Car Show + Market",c:"loc",x:"Downtown CL · 10 AM–1 PM",u:"https://downtowncl.org/annual-events/"},
  {d:"2026-05-19",t:"Social Dance Night",c:"loc",x:"Crystal Lake Park District",u:"https://www.crystallakeparks.org/special-events"},
  {d:"2026-06-01",t:"Chamber Golf Outing",c:"loc",x:"CL Chamber of Commerce",u:"https://business.clchamber.com/events"},
  {d:"2026-06-20",t:"Midsummer Wine Walk",c:"loc",x:"Downtown Crystal Lake",u:"https://downtowncl.org/wine-walk/"},
  {d:"2026-07-05",t:"Independence Parade",c:"loc",x:"Downtown Crystal Lake",u:"https://business.clchamber.com/events"},
];

const C={
  hol: {l:"Holidays",cl:"#B68036",bg:"rgba(182,128,54,0.10)",ic:"✦"},
  lr:  {l:"Lake Roots",cl:"#363A2E",bg:"rgba(54,58,46,0.10)",ic:"⛵"},
  dole:{l:"The Dole",cl:"#8B6F52",bg:"rgba(139,111,82,0.10)",ic:"🏛"},
  sch: {l:"School (D47/155)",cl:"#5B7A6F",bg:"rgba(91,122,111,0.10)",ic:"◆"},
  loc: {l:"Local Events",cl:"#7EA9A1",bg:"rgba(126,169,161,0.12)",ic:"●"},
};
const MN=["January","February","March","April","May","June","July","August","September","October","November","December"];
const DN=["Sun","Mon","Tue","Wed","Thu","Fri","Sat"];
function fk(d){return d.getFullYear()+"-"+String(d.getMonth()+1).padStart(2,"0")+"-"+String(d.getDate()).padStart(2,"0")}
function gw(y,m){const f=new Date(y,m,1),l=new Date(y,m+1,0),w=[];let c=new Date(f);c.setDate(c.getDate()-c.getDay());while(c<=l||c.getDay()!==0){const r=[];for(let i=0;i<7;i++){r.push(new Date(c));c.setDate(c.getDate()+1)}w.push(r);if(c>l&&c.getDay()===0)break}return w}
const GCAL="https://calendar.google.com/calendar/u/0?cid=Y18yNGQ5NDBjZWIxNGVjNGY3ODI3NWUwN2I4Nzc1MGJkNjIxMGMyMzY3N2RlMzkxZGMzMjFkNGZkOWI0MTM3MGUxQGdyb3VwLmNhbGVuZGFyLmdvb2dsZS5jb20";

function App(){
  const[fl,setFl]=useState(new Set(Object.keys(C)));
  const[off,setOff]=useState(0);
  const[op,setOp]=useState(null);
  const pr=useRef(null);
  useEffect(()=>{const h=e=>{if(pr.current&&!pr.current.contains(e.target))setOp(null)};document.addEventListener("click",h);return()=>document.removeEventListener("click",h)},[]);
  const cm=useMemo(()=>{let m=3+off,y=2026;while(m>11){m-=12;y++}while(m<0){m+=12;y--}return{m,y}},[off]);
  const wks=useMemo(()=>gw(cm.y,cm.m),[cm]);
  const em=useMemo(()=>{const r={};EV.forEach(e=>{if(!fl.has(e.c))return;if(!r[e.d])r[e.d]=[];r[e.d].push(e)});return r},[fl]);
  const tg=c=>{setFl(p=>{const n=new Set(p);if(n.has(c))n.delete(c);else n.add(c);return n})};
  const tk=fk(new Date());
  const e=React.createElement;

  return e("div",{style:{minHeight:"100vh",background:"#FCFBE4"}},
    e("div",{style:{background:"#363A2E",padding:"20px 16px 14px",position:"relative",overflow:"hidden"}},
      e("div",{style:{position:"absolute",top:0,left:0,right:0,bottom:0,background:"repeating-linear-gradient(120deg,transparent,transparent 50px,rgba(252,251,228,0.02) 50px,rgba(252,251,228,0.02) 52px)"}}),
      e("div",{style:{position:"relative",zIndex:1}},
        e("div",{style:{display:"flex",alignItems:"center",gap:"8px",marginBottom:"6px"}},
          e("span",{style:{fontSize:"16px",opacity:0.7}},"⛵"),
          e("span",{className:"header-sub",style:{color:"#EFDBB2",fontSize:"9px",letterSpacing:"2.5px",textTransform:"uppercase",fontWeight:600}},"Lake Roots · Community Calendar")),
        e("div",{style:{display:"flex",alignItems:"center",justifyContent:"space-between",marginTop:"6px"}},
          e("button",{className:"nav-btn",onClick:()=>setOff(o=>o-1),style:{background:"rgba(252,251,228,0.08)",border:"1px solid rgba(252,251,228,0.12)",color:"#FCFBE4",borderRadius:"6px",padding:"5px 12px",fontSize:"12px",fontWeight:600}},"\u2190"),
          e("h1",{className:"header-title",style:{color:"#FCFBE4",fontSize:"22px",fontWeight:300,letterSpacing:"1px"}},MN[cm.m]+" ",e("span",{style:{fontWeight:700}},cm.y)),
          e("button",{className:"nav-btn",onClick:()=>setOff(o=>o+1),style:{background:"rgba(252,251,228,0.08)",border:"1px solid rgba(252,251,228,0.12)",color:"#FCFBE4",borderRadius:"6px",padding:"5px 12px",fontSize:"12px",fontWeight:600}},"\u2192")))),
    e("div",{className:"sub-bar",style:{display:"flex",alignItems:"center",justifyContent:"space-between",padding:"8px 16px",background:"#363A2E",borderTop:"1px solid rgba(252,251,228,0.05)"}},
      e("span",{style:{color:"#99BAB4",fontSize:"9px",fontWeight:500}},"Tap any event for details + source link"),
      e("a",{href:GCAL,target:"_blank",rel:"noopener",style:{display:"inline-flex",alignItems:"center",gap:"5px",padding:"4px 12px",borderRadius:"5px",background:"#B68036",color:"#FCFBE4",fontSize:"9px",fontWeight:700,textDecoration:"none"}},"⛵ Subscribe to LR Calendar")),
    e("div",{style:{display:"flex",flexWrap:"wrap",gap:"5px",padding:"8px 12px",background:"#EFDBB2",borderBottom:"2px solid rgba(182,128,54,0.12)"}},
      Object.entries(C).map(([k,v])=>{const on=fl.has(k);return e("button",{key:k,className:"filter-btn",onClick:()=>tg(k),style:{display:"flex",alignItems:"center",gap:"4px",padding:"4px 10px",borderRadius:"16px",border:"2px solid "+v.cl,background:on?v.cl:"transparent",color:on?"#FCFBE4":v.cl,fontSize:"10px",fontWeight:600,transition:"all 0.2s",opacity:on?1:0.45}},e("span",{style:{fontSize:"8px"}},v.ic),v.l)})),
    e("div",{style:{display:"grid",gridTemplateColumns:"repeat(7,1fr)",background:"#363A2E"}},DN.map(d=>e("div",{key:d,className:"day-hdr",style:{padding:"6px 3px",textAlign:"center",color:"#99BAB4",fontSize:"9px",fontWeight:700,letterSpacing:"1.5px",textTransform:"uppercase"}},d))),
    e("div",null,wks.map((wk,wi)=>e("div",{key:wi,style:{display:"grid",gridTemplateColumns:"repeat(7,1fr)",borderBottom:"1px solid rgba(239,219,178,0.3)"}},
      wk.map((day,di)=>{const key=fk(day),isCur=day.getMonth()===cm.m,isT=key===tk,evs=em[key]||[],isWe=di===0||di===6;
        return e("div",{key:di,className:"day-cell",style:{padding:"3px 3px 5px",minHeight:"90px",background:isT?"rgba(182,128,54,0.12)":isWe&&isCur?"rgba(239,219,178,0.15)":isCur?"transparent":"rgba(54,58,46,0.03)",borderRight:di<6?"1px solid rgba(239,219,178,0.25)":"none",opacity:isCur?1:0.2,position:"relative"}},
          e("div",{style:{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:"2px"}},
            e("span",{className:"day-num",style:{display:"inline-flex",alignItems:"center",justifyContent:"center",width:isT?"20px":"auto",height:isT?"20px":"auto",borderRadius:"50%",background:isT?"#B68036":"transparent",color:isT?"#FCFBE4":"#363A2E",fontSize:"11px",fontWeight:isT?700:500}},day.getDate()),
            evs.length>0&&e("div",{style:{display:"flex",gap:"1.5px"}},[...new Set(evs.map(v=>v.c))].map(c=>e("span",{key:c,style:{width:"4px",height:"4px",borderRadius:"50%",background:C[c].cl}})))),
          e("div",{style:{display:"flex",flexDirection:"column",gap:"1.5px"}},
            evs.map((ev,ei)=>{const id=key+"-"+ei,isO=op===id;
              return e("div",{key:ei,ref:isO?pr:null,onClick:x=>{x.stopPropagation();setOp(p=>p===id?null:id)},className:"ev-pill",style:{padding:"1.5px 3px",borderRadius:"3px",background:C[ev.c].bg,borderLeft:"3px solid "+C[ev.c].cl,cursor:"pointer",position:"relative"}},
                e("div",{className:"ev-pill-text",style:{fontSize:"8.5px",fontWeight:700,color:C[ev.c].cl,lineHeight:1.3,whiteSpace:"nowrap",overflow:"hidden",textOverflow:"ellipsis",maxWidth:"90px"}},ev.t),
                isO&&e("div",{className:"popup",style:{position:"absolute",left:di>4?undefined:"0",right:di>4?"0":undefined,top:"100%",marginTop:"4px",background:"#363A2E",color:"#FCFBE4",padding:"10px 14px",borderRadius:"10px",fontSize:"11px",lineHeight:1.5,zIndex:500,whiteSpace:"nowrap",minWidth:"200px",boxShadow:"0 10px 30px rgba(54,58,46,0.5)"}},
                  e("div",{style:{fontWeight:700,fontSize:"12px",marginBottom:"3px",color:"#EFDBB2"}},ev.t),
                  ev.tm&&e("div",{style:{opacity:0.7,fontSize:"10px",marginBottom:"2px"}},"\uD83D\uDD50 "+ev.tm),
                  ev.x&&e("div",{style:{opacity:0.85,marginBottom:ev.u?"6px":"0"}},ev.x),
                  ev.u&&e("a",{href:ev.u,target:"_blank",rel:"noopener",onClick:x=>x.stopPropagation(),style:{display:"inline-flex",alignItems:"center",gap:"4px",color:"#99BAB4",fontSize:"10px",fontWeight:600,borderBottom:"1px solid rgba(153,186,180,0.4)",paddingBottom:"1px"}},"View source \u2192")))})))})})))),
    e("div",{className:"legend",style:{padding:"12px 16px",background:"#363A2E",display:"flex",flexWrap:"wrap",gap:"12px",alignItems:"center",justifyContent:"center"}},
      Object.entries(C).map(([k,v])=>e("div",{key:k,className:"legend-item",style:{display:"flex",alignItems:"center",gap:"5px"}},
        e("span",{style:{width:"9px",height:"9px",borderRadius:"2px",background:v.cl,display:"inline-block",border:"1px solid rgba(252,251,228,0.12)"}}),
        e("span",{style:{color:"#EFDBB2",fontSize:"9px",fontWeight:500}},v.l)))),
    e("div",{style:{padding:"10px 16px",textAlign:"center",background:"#FCFBE4",borderTop:"1px solid rgba(239,219,178,0.3)"}},
      e("div",{style:{color:"#363A2E50",fontSize:"8px",letterSpacing:"0.3px",lineHeight:2}},
        "Sources: ",
        e("a",{href:"https://lakerootscl.com/events",target:"_blank",style:{color:"#363A2E70",textDecoration:"underline"}},"Lake Roots")," · ",
        e("a",{href:"https://www.thedole.org/events-list",target:"_blank",style:{color:"#363A2E70",textDecoration:"underline"}},"The Dole")," · ",
        e("a",{href:"https://www.d47.org/families/calendar/school-year-calendar",target:"_blank",style:{color:"#363A2E70",textDecoration:"underline"}},"D47")," · ",
        e("a",{href:"https://www.d155.org/about/d155-upcoming-events-full-calendar",target:"_blank",style:{color:"#363A2E70",textDecoration:"underline"}},"D155")," · ",
        e("a",{href:"https://downtowncl.org/events/",target:"_blank",style:{color:"#363A2E70",textDecoration:"underline"}},"Downtown CL")," · ",
        e("a",{href:"https://business.clchamber.com/events",target:"_blank",style:{color:"#363A2E70",textDecoration:"underline"}},"CL Chamber")," · ",
        e("a",{href:"https://www.crystallakeparks.org/special-events",target:"_blank",style:{color:"#363A2E70",textDecoration:"underline"}},"CL Park District"))));
}
ReactDOM.createRoot(document.getElementById("root")).render(React.createElement(App));
</script>
</body>
</html>
