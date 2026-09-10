from PIL import Image, ImageDraw, ImageFont
import os, math, zipfile

ROOT = "/mnt/data/KaioVilliers-Profile-FX"
ASSETS = os.path.join(ROOT, "assets")
os.makedirs(ASSETS, exist_ok=True)

FONT = "/usr/share/fonts/truetype/dejavu/DejaVuSans-Bold.ttf"
FONT2 = "/usr/share/fonts/truetype/dejavu/DejaVuSans.ttf"

def F(size, bold=True):
    return ImageFont.truetype(FONT if bold else FONT2, size)

BG = (13,10,8)
ORANGE = (234,97,19)
ORANGE2 = (248,143,34)
GOLD = (251,185,49)
CREAM = (255,227,179)
WHITE = (245,242,236)
MUTED = (150,132,113)
PANEL = (25,18,13)

# ===== Animated HERO =====
W,H = 1200,520
hero_frames=[]
for n in range(20):
    im=Image.new("RGB",(W,H),BG)
    d=ImageDraw.Draw(im)

    # ambient glow made from translucent rectangles/circles on a separate image
    glow=Image.new("RGBA",(W,H),(0,0,0,0))
    gd=ImageDraw.Draw(glow)
    for r in range(260,20,-20):
        a=int(8 + (260-r)*0.12)
        gd.ellipse((850-r,255-r,850+r,255+r),fill=ORANGE2+(min(a,30),))
    glow=glow.filter(ImageFilter.GaussianBlur(24)) if False else glow
    im=Image.alpha_composite(im.convert("RGBA"),glow).convert("RGB")
    d=ImageDraw.Draw(im)

    # grid
    for x in range(0,W,40):
        d.line((x,0,x,H), fill=(55,38,25), width=1)
    for y in range(0,H,40):
        d.line((0,y,W,y), fill=(45,32,24), width=1)

    # animated scan line
    sy=20+n*25
    if sy>H: sy%=H
    d.line((0,sy,W,sy),fill=ORANGE2,width=2)

    # Left typography
    d.text((54,42),"KAIO VILLIERS",font=F(17),fill=CREAM)
    d.text((54,72),"BACKEND / JAVA / 2026",font=F(11,False),fill=MUTED)

    d.text((54,135),"BUILD.",font=F(65),fill=WHITE)
    d.text((54,198),"LEARN.",font=F(65),fill=ORANGE2)
    d.text((54,261),"EVOLVE.",font=F(65),fill=GOLD)

    d.text((58,348),"DESENVOLVEDOR EM FORMAÇÃO",font=F(18),fill=CREAM)
    d.text((58,376),"Java  •  OOP  •  Spring Boot",font=F(16,False),fill=MUTED)

    # status
    d.rounded_rectangle((56,420,385,466),radius=22,fill=PANEL,outline=ORANGE,width=2)
    d.ellipse((74,435,86,447),fill=GOLD)
    d.text((100,430),"LEARNING / BUILDING",font=F(12),fill=CREAM)

    # Right glass-like panel
    x0,y0=645,50
    d.rounded_rectangle((x0,y0,1146,470),radius=34,fill=(23,17,13),outline=CREAM,width=2)
    d.rounded_rectangle((x0+18,y0+18,1128,452),radius=28,outline=ORANGE2,width=1)

    cx,cy=895,260
    # orbit rings
    for rr,col in [(175,ORANGE),(145,ORANGE2),(112,GOLD)]:
        d.ellipse((cx-rr,cy-rr,cx+rr,cy+rr),outline=col,width=2)

    # central core
    d.ellipse((cx-78,cy-78,cx+78,cy+78),fill=(15,11,9),outline=GOLD,width=3)
    d.text((cx-47,cy-46),"KV",font=F(58),fill=CREAM)
    d.text((cx-61,cy+31),"BACKEND",font=F(12),fill=MUTED)

    # orbiting node
    angle=(2*math.pi*n/20)
    px=cx+175*math.cos(angle)
    py=cy+175*math.sin(angle)
    d.ellipse((int(px)-7,int(py)-7,int(px)+7,int(py)+7),fill=GOLD)
    d.ellipse((int(px)-15,int(py)-15,int(px)+15,int(py)+15),outline=ORANGE2,width=2)

    # floating labels
    tags=[("JAVA",682,112,ORANGE),("SPRING BOOT",1000,112,GOLD),
          ("OOP",680,385,ORANGE2),("SQL",1018,385,CREAM)]
    for txt,x,y,col in tags:
        d.rounded_rectangle((x,y,x+125,y+34),radius=13,fill=(30,22,16),outline=col,width=2)
        d.text((x+12,y+8),txt,font=F(10),fill=col)

    # circuit traces
    circuits=[[(700,190),(760,190),(795,155)],
              [(1090,190),(1030,190),(1000,155)],
              [(700,330),(760,330),(795,365)],
              [(1090,330),(1035,330),(1000,365)]]
    for pts in circuits:
        d.line(pts,fill=ORANGE,width=2)
        for qx,qy in (pts[0],pts[-1]):
            d.ellipse((qx-4,qy-4,qx+4,qy+4),fill=GOLD)

    # footer telemetry
    d.text((54,492),"01 IDENTITY   02 STACK   03 PROJECT LAB   04 ANALYTICS",font=F(10),fill=MUTED)
    d.text((1080,492),f"FX_{n+1:02d}",font=F(10),fill=GOLD)

    hero_frames.append(im)

hero_path=os.path.join(ASSETS,"hero.gif")
hero_frames[0].save(hero_path,save_all=True,append_images=hero_frames[1:],duration=130,loop=0,optimize=True)

# ===== Animated STACK =====
W2,H2=1100,330
stack_frames=[]
tech=[("JAVA","01"),("SPRING BOOT","02"),("OOP","03"),("SQL","04"),("GIT","05"),("REST API","06")]
for n in range(18):
    im=Image.new("RGB",(W2,H2),BG)
    d=ImageDraw.Draw(im)
    d.rounded_rectangle((8,8,1092,322),radius=28,fill=PANEL,outline=CREAM,width=2)
    d.text((32,28),"// TECHNOLOGY MATRIX",font=F(15),fill=CREAM)
    d.text((32,54),"core modules / continuous learning",font=F(11,False),fill=MUTED)
    for i,(label,num) in enumerate(tech):
        x=25+i*178
        active=((n//3)%len(tech))==i
        col=GOLD if active else ORANGE
        d.rounded_rectangle((x,105,x+155,285),radius=22,fill=(29,21,16),outline=col,width=2 if active else 1)
        d.ellipse((x+51,123,x+103,175),fill=(15,11,9),outline=col,width=2)
        d.text((x+67,134),num,font=F(14),fill=CREAM)
        d.text((x+12,190),label,font=F(10),fill=col)
        pct=45 + i*8 + ((n*7)%14)
        d.text((x+12,220),f"{pct:02d}%  BUILDING",font=F(8),fill=MUTED)
        d.rectangle((x+12,244,x+143,248),fill=(66,45,28))
        d.rounded_rectangle((x+12,244,x+12+131*pct/100,248),radius=2,fill=col)
    stack_frames.append(im)

stack_path=os.path.join(ASSETS,"stack.gif")
stack_frames[0].save(stack_path,save_all=True,append_images=stack_frames[1:],duration=145,loop=0,optimize=True)

# ===== Decorative SVG =====
svg='''<svg xmlns="http://www.w3.org/2000/svg" width="1200" height="180" viewBox="0 0 1200 180">
<defs>
<linearGradient id="g" x1="0" x2="1"><stop stop-color="#EA6113"/><stop offset=".5" stop-color="#F88F22"/><stop offset="1" stop-color="#FBB931"/></linearGradient>
<filter id="s"><feGaussianBlur stdDeviation="4" result="b"/><feMerge><feMergeNode in="b"/><feMergeNode in="SourceGraphic"/></feMerge></filter>
</defs>
<rect width="1200" height="180" rx="28" fill="#0D0A08"/>
<path d="M45 120H1155" stroke="#FBB931" stroke-opacity=".12"/>
<path d="M45 55H1155" stroke="#FBB931" stroke-opacity=".08"/>
<path d="M70 90H300L340 55H520" stroke="url(#g)" stroke-width="3" fill="none" filter="url(#s)"/>
<path d="M680 55H860L900 90H1130" stroke="url(#g)" stroke-width="3" fill="none" filter="url(#s)"/>
<g fill="#FBB931"><circle cx="300" cy="90" r="5"/><circle cx="340" cy="55" r="5"/><circle cx="520" cy="55" r="5"/><circle cx="680" cy="55" r="5"/><circle cx="860" cy="55" r="5"/><circle cx="900" cy="90" r="5"/></g>
<text x="70" y="82" fill="#FFE3B3" font-family="Arial" font-size="14" font-weight="700">SYSTEM LAYER</text>
<text x="70" y="106" fill="#9E8E7A" font-family="Arial" font-size="12">JAVA → OOP → SPRING BOOT → DATA → ARCHITECTURE</text>
<text x="680" y="82" fill="#FFE3B3" font-family="Arial" font-size="14" font-weight="700">BUILD STATE</text>
<text x="680" y="106" fill="#9E8E7A" font-family="Arial" font-size="12">LEARNING • BUILDING • REFACTORING</text>
</svg>'''
with open(os.path.join(ASSETS,"system-layer.svg"),"w",encoding="utf-8") as f:
    f.write(svg)

# ===== README =====
readme='''<div align="center">

<img src="./assets/hero.gif" width="100%" alt="Kaio Villiers — Backend Developer">

<br><br>

<img src="./assets/stack.gif" width="100%" alt="Technology Matrix">

</div>

---

# `01 / IDENTITY`

<table>
<tr>
<td width="58%" valign="top">

### **DESENVOLVEDOR EM FORMAÇÃO · BACKEND JAVA**

Formado em **Análise e Desenvolvimento de Sistemas**.

Meu foco atual é transformar fundamentos em prática através de **Java, Programação Orientada a Objetos e Spring Boot**.

> Não estou tentando parecer pronto.  
> Estou construindo a base para chegar lá.

</td>

<td width="42%" valign="top">

```text
╔══════════════════════════════╗
║ PROFILE STATUS      ● ONLINE ║
╠══════════════════════════════╣
║ ADS                ✓ DONE    ║
║ JAVA               ◉ DEEP    ║
║ OOP                ◉ DEEP    ║
║ SPRING BOOT        ◉ BUILD   ║
║ SQL                ◉ BUILD   ║
║ PROJECTS           ◉ BUILD   ║
║ ARCHITECTURE       ○ NEXT   ║
╚══════════════════════════════╝
```

</td>
</tr>
</table>

---

<div align="center">

<img src="./assets/system-layer.svg" width="100%" alt="System Layer">

</div>

---

# `02 / ABOUT_ME`

```java
public final class Kaio {

    final String name = "Kaio Villiers";
    final String focus = "Backend Development";

    final String[] core = {
        "Java",
        "Object-Oriented Programming",
        "Spring Boot",
        "SQL"
    };

    void evolve() {
        study();
        practice();
        build();
        refactor();
    }
}
```

---

# `03 / TECH STACK`

<div align="center">

<img src="https://skillicons.dev/icons?i=java,spring,git,github,postgresql,vscode&theme=dark" alt="Java, Spring, Git, GitHub, PostgreSQL, VS Code">

<br><br>

<img src="https://img.shields.io/badge/JAVA-EA6113?style=for-the-badge&logo=openjdk&logoColor=FFE3B3">
<img src="https://img.shields.io/badge/SPRING_BOOT-FBB931?style=for-the-badge&logo=springboot&logoColor=0D0A08">
<img src="https://img.shields.io/badge/OOP-F88F22?style=for-the-badge&logo=openjdk&logoColor=0D0A08">
<img src="https://img.shields.io/badge/SQL-EA6113?style=for-the-badge&logo=postgresql&logoColor=FFE3B3">

</div>

<details>
<summary><b>▸ OPEN / STUDY MATRIX</b></summary>

| MODULE | CURRENT FOCUS | STATE |
|:---|:---|:---:|
| `JAVA` | linguagem, coleções, exceções, generics | `DEEP` |
| `OOP` | abstração, encapsulamento, herança, polimorfismo | `DEEP` |
| `SPRING BOOT` | APIs REST e backend | `BUILDING` |
| `SQL` | consultas e persistência | `BUILDING` |
| `GIT` | versionamento | `ACTIVE` |
| `ARCHITECTURE` | próximo ciclo de estudos | `NEXT` |

</details>

---

# `04 / PROJECT LAB`

<table>
<tr>
<td width="33%" valign="top">

### `01 / JAVA`

☕ Fundamentos, lógica e OOP.

`COLLECTIONS`  
`EXCEPTIONS`  
`OOP`

</td>

<td width="33%" valign="top">

### `02 / SPRING`

⚙️ Construções de backend.

`REST`  
`VALIDATION`  
`PERSISTENCE`

</td>

<td width="33%" valign="top">

### `03 / NEXT`

◈ Próxima evolução.

`TESTS`  
`ARCHITECTURE`  
`CLOUD`

</td>
</tr>
</table>

> **PROJECT STATUS:** `BUILDING`

---

# `05 / GITHUB ANALYTICS`

<div align="center">

<img src="https://github-readme-stats.vercel.app/api?username=KaioDias30&show_icons=true&hide_border=true&bg_color=0D0A08&title_color=FBB931&text_color=FFE3B3&icon_color=F88F22&ring_color=EA6113&rank_icon=github" width="49%">

<img src="https://github-readme-streak-stats.herokuapp.com/?user=KaioDias30&hide_border=true&background=0D0A08&ring=EA6113&fire=FBB931&currStreakLabel=FFE3B3&sideLabels=FFE3B3&dates=9E8E7A&currStreakNum=FFE3B3&sideNums=FFE3B3" width="49%">

<br><br>

<img src="https://github-readme-stats.vercel.app/api/top-langs/?username=KaioDias30&layout=donut&hide_border=true&bg_color=0D0A08&title_color=FBB931&text_color=FFE3B3" width="45%">

</div>

---

# `06 / CONNECT`

<div align="center">

<a href="mailto:Kaiovilliers@gmail.com">
<img src="https://img.shields.io/badge/EMAIL-EA6113?style=for-the-badge&logo=gmail&logoColor=FFE3B3">
</a>

&nbsp;

<a href="https://www.instagram.com/kaiovilliers">
<img src="https://img.shields.io/badge/INSTAGRAM-F88F22?style=for-the-badge&logo=instagram&logoColor=0D0A08">
</a>

<br><br>

`KAIO VILLIERS` · `JAVA` · `BACKEND` · `SPRING BOOT`

</div>

---

<div align="center">

### `SYSTEM ONLINE`

`LEARNING` → `BUILDING` → `REFACTORING` → `EVOLVING`

</div>
'''
with open(os.path.join(ROOT,"README.md"),"w",encoding="utf-8") as f:
    f.write(readme)

with open(os.path.join(ROOT,"LEIA-ME.txt"),"w",encoding="utf-8") as f:
    f.write("""1) No repositório KaioDias30/KaioDias30, substitua o conteúdo do README.md pelo README.md deste pacote.
2) Crie a pasta assets.
3) Envie para assets: hero.gif, stack.gif e system-layer.svg.
4) Faça o commit.
5) O visual animado vem dos GIFs. O <details> cria interação abrir/fechar.
6) GitHub não executa JavaScript/CSS arbitrário em README, então animação e efeitos precisam ser imagens/GIFs.
""")

zip_path="/mnt/data/KaioVilliers-Profile-FX.zip"
with zipfile.ZipFile(zip_path,"w",zipfile.ZIP_DEFLATED) as z:
    for root,dirs,files in os.walk(ROOT):
        for fn in files:
            p=os.path.join(root,fn)
            z.write(p,os.path.relpath(p,ROOT))

print(zip_path)
print("Arquivos:", os.listdir(ASSETS))
