---
title: CSS ๊ธฐ์ด
toc: true
toc_sticky: true
toc_label: ๋ชฉ์ฐจ
published: true
categories:
    - HTML
tags:
    - HTML
    - Front-end
    - CSS
---
# ๐ CSS๋?
* `Cascading Style Sheets`์ ์ฝ์๋ก `HTML`๋ก ๋ง๋  ์น ๋ฌธ์ ๋ผ๋์ ๋์์ธ์ ์ ์ฉํ๊ธฐ ์ํ ๊ฒ

## ๐ธ ์ ์ฉ ๋ฐฉ๋ฒ
1. ํ๊ทธ์ ๋ฐ๋ก ์คํ์ผ ์ ์ฉ - ์ ์์<br>
2. `head` ์์์ ์คํ์ผ ์ ์ฉ<br>
3. ์ธ๋ถํ์ผ๋ก ์คํ์ผ ์ ์ฉ<br><br><br>

# ๐ ๊ธฐ๋ณธ ๋ฌธ๋ฒ
```css
ํ๊ทธ ๋์
{
    ์์ฑ: ๊ฐ;
    ์์ฑ: ๊ฐ; ...
}

*
{
    HTML ๋ด์ฉ ์ ์ฒด ์ ํ
}

h2
{
    color: blue;
    background-color: skyblue;
    ๋ชจ๋  h2 ํ๊ทธ์ ์ ์ฉ
} 

#p1
{
    id="p1" ์ธ id ๋ชจ๋ ์ ํ
}

.p2
{
    class="p2" ์ธ class ๋ชจ๋ ์ ํ
}

#footer p
{
    footer ์์ ์๋ p ํ๊ทธ์๋ง ์ ์ฉ
}

/* ํ์ดํผ๋งํฌ ํ๊ทธ์๋ง ๋ถ๋ถ์ ์ผ๋ก ์ ์ฉํ๋ ๊ฒ๋ ๊ฐ๋ฅํ๋ค */
a:link, a:visited
{
	/* ํ์ดํผ๋งํฌ์, ๋ฐฉ๋ฌธํ ํ์ดํผ๋งํฌ์ ๋ณ๊ฒฝ */
	color: black;
}

a:hover
{
	/* ํ์ดํผ๋งํฌ์ ๋ง์ฐ์ค๋ฅผ ์ฌ๋ฆฌ๋ฉด */
	color: white;
	background-color: black;
}
```

* `<p id="p1">`, `<span class="p2">` ์ ๊ฐ์ด ์ง์ ํ๋ฉฐ `id`๋ ๋์ ์์ญ์ ์ง์ ํ  ๋ ์ฃผ๋ก ์ฌ์ฉํ๊ณ  `class`๋ ์ข์ ์์ญ์ ์ง์ ํ  ๋ ์ฃผ๋ก ์ฌ์ฉํ๋ค๊ณ  ํ๋ค.
<br><br>

# ๐ CSS ๊พธ๋ฏธ๊ธฐ ์์ฑ๋ค

```css
p
{
	/* ๋ฐฐ์น */
	display: block; ์ ์ฒด์์ญ(๋ฌธ๋จ์์ญ) : ๋ธ๋ก๋ ๋ฒจ
	display: inline; ์์์์ญ : ์ธ๋ผ์ธ ๋ ๋ฒจ(๊ฐ๋ก๋ก ๋ถ์)
	display: inline-block; ๋ฉ๋ด ๋ง๋ค๋ ์ธ๋ผ์ธ ๋ธ๋ก์ด ํธํ๋ค
	display: inline-block;
    
	/* ๋ฐ์คํฌ๊ธฐ ๊ณ์ฐ box-sizing */
    /* ์น ํ์ด์ง์ ๋ด์ฉ๋ฌผ๋ค์ ๋ฐฐ์นํ  ๋ ๋ฐ์คํฌ๊ธฐ ๊ณ์ฐํ ๊ฒ์ ๋ฐํ์ผ๋ก ๋ฐฐ์นํ๋ ๊ฒ์ด ์ข๋ค */
	box-sizing: border-box; ๋ด์ฉ ์์ฌ๋ฐฑ ํ๋๋ฆฌ์  ํฌํจํด์ ํฌ๊ธฐ ๊ณ์ฐ
	box-sizing: content-box; ๋ด์ฉ(๊ธฐ๋ณธ๊ฐ)๋ฌผ๋ง ํฌํจํด์ ํฌ๊ธฐ ๊ณ์ฐ
    
	/* ๋๋น */
	width: 600px;
	
	/* ๋์ด */
	height: 50%;
 
    /* ์ ์ฌ๋ฐฑ 10px */
	padding: 10px;
	
	/* ๋ฐ ์ฌ๋ฐฑ */
	margin: 50px;
    
	/* ๋ฐฐ๊ฒฝ์ */
	background-color: silver;
	background-color: #0000ff;
	background-color: rgb(255, 0, 0);
    
    color: rgba(255, 0, 0, 0.5);
    color: #ff0000;
    
    font-family: "๋ฐํ";
    
    font-size: 20px;
    font-size: 3em;
    /* ์ ๋ํฌ๊ธฐ(์ง์ ๋ ํฌ๊ธฐ) : ์๋ํฌ๊ธฐ(์๋์ ์ธ ๊ธ์ํฌ๊ธฐ) */
    /* ํฌ์ธํธ pt, ํฝ์ px, em ๋๋ฌธ์๋ฅผ ๊ธฐ์ค์ผ๋ก ๋น์จ๊ฐ ์ ์ฉ, rem 1em ๋น์จ๊ฐ ์ ์ฉ, ex ์๋ฌธ์๋ฅผ ๊ธฐ์ค์ผ๋ก ์ ์ฉ */
    
    font-style: italic;
    
    font-weight: bold;
    
    /* ๊ธ์ ๊ฐ์ด๋ฐ ์ ๋ ฌ */
    text-align: center;
    
    line-height: 100%;
    
    text-decoration: overline;
    
    /* ๊ทธ๋ฆผ์ ํจ๊ณผ */
    text-shadow: 5px 5px 3px black; 
    
    /* ๊ธ์ ํ๋์ฌ์ด ๊ฐ๊ฒฉ*/
    letter-spacing: 0.5em; 
    
    /* ๋จ์ด ์ฌ์ด ๊ฐ๊ฒฉ */
    word-spacing: 50px; 
    
    /* ๋ชฉ๋ก ๋ค์ฌ์ฐ๊ธฐ ๋ด์ด์ฐ๊ธฐ inside/outside */
    list-style-position: outside;
    
    /* ๋ชฉ๋ก ์ (๋ถ๋ฆฟ) ๋์  ์ด๋ฏธ์ง ์ฌ์ฉ */
    list-style-image: url("1.jpeg");
    
    /* ํ ์ ๋ชฉ ์์น ์ง์  caption-side: top/bottom; */
	caption-side: top;
    
	/* ํ ํ๋๋ฆฌ์  ๊ทธ๋ ค์ฃผ๊ธฐ border: ํ๋๋ฆฌ์  ๋๊ป ํ๋๋ฆฌ์  ๋ชจ์ ํ๋๋ฆฌ์  ์ */
	border: 1px solid black;
    border-style: dotted;
	border-color: blue;
    
	/* ์(์นธ) ์ฌ์ด์ ์ฌ๋ฐฑ */
	border-spacing: 10px;
	
	/* ํ์ ์ ํ๋๋ฆฌ ํฉ์น๊ธฐ */
	border-collapse: collapse;
    
    /* ๋ชจ์๋ฆฌ ๋ฅ๊ธ๊ฒ */
	border-radius: 50%;
    
    /* ๋ฐ์ค ๊ทธ๋ฆผ์ (12์๋ถํฐ ์๊ณ๋ฐฉํฅ์ผ๋ก) ์์ */
	box-shadow: 5px 5px 15px 5px green;
 
	/* ์ด์ธ๋ฆผ ๋ฐฐ์นํ๋ ๋ฒ */
	float: left; /* ์ด๋ฏธ์ง๋ฅผ ์ผ์ชฝ์ผ๋ก ๊ธฐ์ค์ผ๋ก ๊ธ์ ์ด์ธ๋ฆผ ๋ฐฐ์น */
	float: right; /* ์ด๋ฏธ์ง ์ค๋ฅธ์ชฝ์ผ๋ก ๊ธฐ์ค์ผ๋ก ๊ธ์ ์ด์ธ๋ฆผ ๋ฐฐ์น */
 
	/* ์ด์ธ๋ฆผ ๋ฐฐ์น ํด์  */
	/* ์๊น ์ค์ ํ ๋ฐฉํฅ ์ค ํด์ ํ๊ณ  ์ถ์ ๋ฐฉํฅ์ ์๋ ฅํด์ค */
	clear: left;
	clear: right;
	clear: both;  
    
    /* ๋ฐฐ๊ฒฝ ์ด๋ฏธ์ง ์ฝ์ */
	/* ์ด๋ฏธ์ง ํ๊ทธ์์ ์๋ณด์ด๋ฉด ์คํ์ผ์ํธ์์ ์ด๋ฏธ์ง๋ฅผ ๋ฃ์ ๊ฒ์ด๋ค. */
	background-image: url("dot.png");
	background-repeat: repeat; /* ์ด๋ฏธ์ง ๋ฐ๋ํ ๋ชจ์์ผ๋ก ๋ฐ๋ณต */
	background-repeat: repeat-x; /* x์ถ๋ง ๋ฐ๋ณต */
	background-repeat: repeat-y; /* y์ถ๋ง ๋ฐ๋ณต */
	background-repeat: no-repeat; /* ๋ฐ๋ณต ์ํจ */ 
	background-position: center; /* ๋ฌธ๋จ ๊ฐ์ด๋ฐ๋ก ์ ๋ ฌ */
	background-position: bottom right; /* ๋๊ฐ ๋ฃ์ผ๋ฉด ์ค๋ฅธ์ชฝ ์๋์ ๊ฐ์ด ์ ๋ ฌ๋จ */
	background-position: top left;
	background-size: 50px 50px;
}
```
<br>

## โ๏ธ CSS ๊พธ๋ฏธ๊ธฐ ์์์
```css
*
{
	/* body ์์ญ์ ๊ธฐ๋ณธ์ ์ธ ์ฌ๋ฐฑ์ด ์กฐ๊ธ์ฉ ์๋๋ฐ ์ด๊ฑธ ๋ค 0์ผ๋ก ๋ง๋ค๊ณ  ์์ํ๋ค. */
	margin: 0px;
	padding: 0px;
	
	/* ํ๋๋ฆฌํฌํจ ๋๋น ๊ณ์ฐํ๊ธฐ */
	box-sizing: border-box;
}
```

* ์์ ๊ฐ์ด ์ด๊ธฐํํ๊ณ  ์์ํ๋ ๊ฒ์ด ์ข๋ค.
