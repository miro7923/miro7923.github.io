---
title: OS) Virtual Memory
toc: true
toc_sticky: true
toc_label: ๋ชฉ์ฐจ
published: true
categories:
    - Operating System
tags:
    - CS
    - OS
    - VirtualMemory
---
# ๐ Demain Paging
* ์ค์ ๋ก ํ์ํ  ๋ `page`๋ฅผ ๋ฉ๋ชจ๋ฆฌ์ ์ฌ๋ฆฌ๋ ๊ฒ
* ์๋๋ฉด ํ๋ก๊ทธ๋จ์ ๋๋ถ๋ถ์ ์ฝ๋๋ (๊ฑฐ์ ๋ฐ์ํ์ง ์๋ ์น๋ช์ ์ธ) ์ค๋ฅ๋ฅผ ํด๊ฒฐํ๊ธฐ ์ํ ์ฝ๋๋ผ ํ์์๋ ์ฐ์ง์ง ์๋ ๋ถ๋ถ์ด ๋๋ค์๋ค. ๊ทธ๋์ ์ด๊ฑธ ๋ค ๋ฉ๋ชจ๋ฆฌ์ ์ฌ๋ ค ๋์ผ๋ฉด ๋ฉ๋ชจ๋ฆฌ ๊ณต๊ฐ๋ง ์ฐจ์งํ๊ณ  ๋นํจ์จ์ ์ด๋ค.
* ๊ทธ๋์ ํ๋ก์ธ์ค ๋์์ ์ค์ ๋ก ํ์ํ `page`๋ง ๋ฉ๋ชจ๋ฆฌ์ ์ฌ๋ฆฌ๋ ๊ฒ์ด ํจ์จ์ ์ด๋ค.
* ์ฅ์ 
    * `I/O` ์์ ๊ฐ์
    * ๋ฉ๋ชจ๋ฆฌ ์ฌ์ฉ๋ ๊ฐ์
    * ๋น ๋ฅธ ์๋ต ์๊ฐ
    * ๋ ๋ง์ ์ฌ์ฉ์ ์์ฉ
<br><br>

* ๋ฌผ๋ฆฌ์  ๋ฉ๋ชจ๋ฆฌ์ `page`๊ฐ ์ฌ๋ ค์ง `page entry`์์ `Valid/Invalid bit`๋ฅผ ์ฌ์ฉํด ํ์ฌ ๋ฉ๋ชจ๋ฆฌ์ `page`๊ฐ ์ฌ๋ ค์ ธ ํ์ธํ๋ค. ๋ง์ฝ ์ด๋ค ๋ฉ๋ชจ๋ฆฌ ์ฃผ์์ ์ ๊ทผํ์ ๋ ํด๋น ์์น๊ฐ `Invalid bit`๋ก ์ธํ๋์ด ์์ผ๋ฉด `page fault`๊ฐ ์ผ์ด๋ฌ๋ค๊ณ  ํ๋ค.
* `page fault`๊ฐ ์ผ์ด๋๋ฉด ๋์คํฌ์ ์ง์  ์ ๊ทผํด์ ํด๋น ํ์ด์ง๋ฅผ ๊ฐ์ ธ์์ผ ํ๋๋ฐ ์ด๋ ๋ฉ๋ชจ๋ฆฌ์ ์ ๊ทผํด์ ๊ฐ์ ธ์ค๋ ๊ฒ์ ๋นํด ์๊ฐ์ด ์์ฃผ ๋ง์ด ๊ฑธ๋ฆฐ๋ค.<br><br>

# Replacement Algorithm
## Page replacement
* ์๋ก์ด ํ์ด์ง๋ฅผ ์ฌ๋ฆด ๊ณต๊ฐ์ด ์์ผ๋ฉด ์ฌ๋ ค์ ธ ์๋ ํ์ด์ง ์ค ์ด๋ค ๊ฒ์ ์ซ์๋ผ ์ง ๊ฒฐ์ ํด์ผ ํ๋ค.
* ์ด๋ฅผ ์ํ ์ฌ๋ฌ ์๊ณ ๋ฆฌ์ฆ์ด ์๋๋ฐ ๋์ฒด๋ก `page fault`๋ฅผ ์ต์ํํ๊ธฐ ์ํด ๊ณง๋ฐ๋ก ์ฌ์ฉ๋์ง ์์ ํ์ด์ง๋ฅผ ์ซ์๋ด๋ ํํ๋ก ๊ตฌํ๋์ด ์๋ค.
    
## Optimal Algorithm
* ๊ฐ์ฅ ๋จผ ๋ฏธ๋์ ์ฌ์ฉ๋ (๋น์ฅ ์ฌ์ฉ๋์ง ์์) ํ์ด์ง๋ฅผ ์ซ์๋ด๋ ์๊ณ ๋ฆฌ์ฆ
* ๋ฏธ๋์ ์ฐธ์กฐ๋  ํ์ด์ง๋ฅผ ๋ฏธ๋ฆฌ ์์ธกํ  ์ ์๋ค๋ ๊ฐ์ ํ์ ์ฌ์ฉํ  ์ ์๋ ์๊ณ ๋ฆฌ์ฆ์ด๋ค. ํ์ง๋ง ๋ฏธ๋๋ฅผ ์๋ ๊ฒ์ ๋ถ๊ฐ๋ฅํ๊ธฐ ๋๋ฌธ์ ์ค์  ์์คํ์ ์ฌ์ฉ์ ๋ถ๊ฐ๋ฅํ๋ค.

## FIFO (First In First Out) Algorithm
* ๋จผ์  ๋ค์ด์จ ๊ฒ์ ๋จผ์  ๋ด์ซ๋ ์๊ณ ๋ฆฌ์ฆ
* ์ค์  ์์คํ์ ์ฌ์ฉ๋๋ ์๊ณ ๋ฆฌ์ฆ์ด๋ค.
* ๋จผ์  ๋ค์ด์จ ๊ฒ์ ๋ด์ซ๋ ๊ณผ์ ์์ ํ์ด์ง ํ๋ ์ ํฌ๊ธฐ๋ฅผ ๋๋ ธ๋๋ฐ ์คํ๋ ค `page fault`๊ฐ ๋์ด๋ ์ฑ๋ฅ์ด ํ๋ฝํ๋ ๊ฒฝ์ฐ๊ฐ ์๊ธธ ์ ์๋ค.

## LRU (Least Recently Used) Algorithm
* ๊ฐ์ฅ ์ค๋ ์ ์ ์ฐธ์กฐ๋ ๊ฒ์ ์ง์ฐ๋ ์๊ณ ๋ฆฌ์ฆ
* ์ค์  ์์คํ์์ ๋ง์ด ์ฌ์ฉ๋๋ค.
* ์ด๋ค ํ์ด์ง๊ฐ ์ฐธ์กฐ๋๋ฉด ๊ทธ๊ฒ์ ์์๋ฅผ ๋งจ ์์ผ๋ก ๋ฐ๊ฟ์ฃผ๊ธฐ๋ง ํ๋ฉด ๋๊ธฐ ๋๋ฌธ์ ๋งํฌ๋ ๋ฆฌ์คํธ(Linked list) ํํ๋ก ๊ตฌํํ๋ค. ์๊ฐ๋ณต์ก๋๋ `O(1)`

## LFU (Least Frequently Used) Algorithm
* ์ฐธ์กฐ ํ์(reference count)๊ฐ ๊ฐ์ฅ ์ ์ ํ์ด์ง๋ฅผ ์ง์ฐ๋ ์๊ณ ๋ฆฌ์ฆ
    * ์ต์  ์ฐธ์กฐ ํ์ ํ์ด์ง๊ฐ ์ฌ๋ฌ ๊ฐ ์๋ ๊ฒฝ์ฐ
        * ๋ด์ซ์ ํ์ด์ง๋ฅผ ์์๋ก ์ ์ ํ๋ค.
        * ์ฑ๋ฅ ํฅ์์ ์ํด ๊ฐ์ฅ ์ค๋ ์ ์ ์ฐธ์กฐ๋ ํ์ด์ง๋ฅผ ์ง์ฐ๊ฒ ๊ตฌํํ  ์๋ ์๋ค.
    * ์ฅ๋จ์ 
        * LRU์ฒ๋ผ ์ง์  ์ฐธ์กฐ ์์ ๋ง ๋ณด๋ ๊ฒ์ด ์๋๋ผ ์ฅ๊ธฐ์ ์ธ ์๊ฐ ๊ท๋ชจ๋ฅผ ๋ณด๊ธฐ ๋๋ฌธ์ ํ์ด์ง์ ์ธ๊ธฐ๋๋ฅผ ์ข ๋ ์ ํํ ๋ฐ์ํ  ์ ์๋ค.
        * ์ฐธ์กฐ ์์ ์ ์ต๊ทผ์ฑ์ ๋ฐ์ํ์ง ๋ชป ํ๋ค.(๋ง์ฝ 1์ด ์ ์ ์ฒ์์ผ๋ก ์ฐธ์กฐ ๋์๋ค๋ฉด ์ธ๊ธฐ๋๊ฐ ๋ฎ์ ๊ฒ์ผ๋ก ๊ฐ์ฃผํ๊ณ  ๋ด์ซ๊ฒ ๋๋ค)
        * LRU๋ณด๋ค ๊ตฌํ์ด ๋ณต์กํ๋ค.
* ์ด๋ค ํ์ด์ง๊ฐ ์ฐธ์กฐ๋๋ฉด ์ด์ ์ ์ฐธ์กฐ๋ ํ์ด์ง๋ค์ ์ฐธ์กฐ ํ์์ ํ๋์ฉ ๋น๊ตํด์ ์์๋ฅผ ์ ํด์ค์ผ ํ๋ค. ๊ทธ๋์ LRU์ฒ๋ผ ๋งํฌ๋ ๋ฆฌ์คํธ ํํ๋ก ๊ตฌํํ๋ฉด ์์๋ฅผ ์ฌ์ค์  ํ๋ ๋ฐ ๋๋ฌด ์ค๋ ๊ฑธ๋ฆฐ๋ค. ๊ทธ๋์ ์ต์ํ(Min heap) ํํ๋ก ๊ตฌํํ๋ค. ์ฐธ์กฐ ํ์๊ฐ ๊ฐ์ฅ ์ ์ ํ์ด์ง๊ฐ ํ์ ์ต์๋จ์ ์๋ ๊ฒ์ด๋ค. ์๊ฐ๋ณต์ก๋๋ `O(log n)`<br><br>

# ์บ์ ๊ธฐ๋ฒ
* ํ์ ๋ ๋น ๋ฅธ ๊ณต๊ฐ(=์บ์ฌ)์ ์์ฒญ๋ ๋ฐ์ดํฐ๋ฅผ ์ ์ฅํด ๋์๋ค๊ฐ ํ์ ์์ฒญ์ ์บ์ฌ๋ก๋ถํฐ ์ง์  ์๋น์คํ๋ ๋ฐฉ์
* `paging system` ์ธ์๋ `cache memory`, `buffer caching`, `Web caching` ๋ฑ ๋ค์ํ ๋ถ์ผ์์ ์ฌ์ฉ๋๋ค.
* ๊ทธ๋ฐ๋ฐ `paging system`์์๋ `page fault`๊ฐ ์๊ฒจ์ ๋์คํฌ์์ ํ์ด์ง๋ฅผ ๊ฐ์ ธ์์ผ ํ  ๋์๋ง `OS`๊ฐ ๊ด์ฌํ๊ณ  ํ์ด์ง๊ฐ ๋ฉ๋ชจ๋ฆฌ์ ์์ด์ ๋์คํฌ๊น์ง ๊ฐ ํ์๊ฐ ์์ ๋์ `OS`๊ฐ ๊ด์ฌํ์ง ์๋๋ค. ๊ทธ๋์ `OS`๋ ํ์ด์ง์ ๋ฉ๋ชจ๋ฆฌ ์์ฅ ์๊ฐ ์ ๋๋ง ์๊ณ  ์์ง ๋ฉ๋ชจ๋ฆฌ์ ์ฌ๋ผ๊ฐ ์ดํ์ ์ฐธ์กฐ ์๊ฐ๊ณผ ๋น๋ ๊ฐ์ ๊ฒ์ ์ ์ ์๋ค. ๊ทธ๋์ ์ฌ์ค ์์์ ์ค๋ชํ๋ LRU์ LFU ๊ฐ์ ์๊ณ ๋ฆฌ์ฆ๋ค์ `OS`์ `paging system`์์๋ ์ฌ์ฉํ  ์ ์๋ค.

## Clock Algorithm
* ๊ทธ ๋์  ๋น์ทํ ํจ๊ณผ๋ฅผ ๋ผ ์ ์๋ `Clock Algorithm`์ ์ฌ์ฉํ๋ค.
* ์๊ณ์์ ์ด์นจ์ด ๋์๊ฐ๋ ๊ฒ์ฒ๋ผ ํฌ์ธํฐ๊ฐ `page entry`๋ฅผ ๋๋ฉด์ ๊ต์ฒดํ  ํ์ด์ง๋ฅผ ์ ์ ํ๋ ๊ฒ์ด๋ค.
* ํ ๋ฐํด ๋๋ฉด์ `Reference bit`๊ฐ 1์ด๋ฉด ๊ทธ๋์ ์ฌ์ฉ๋ ๊ฒ์ผ๋ก ๋ณด๊ณ  0์ผ๋ก ๋ฐ๊พธ๊ณ  ๋์ด๊ฐ๊ณ  0์ด๋ฉด ๊ทธ๋์ ์ฌ์ฉํ์ง ์์ ๊ฒ์ผ๋ก ๊ฐ์ฃผํด ์ซ์๋ธ๋ค.<br><br>

# Page Frame์ Allocation
* ๋ฉ๋ชจ๋ฆฌ ์ฐธ์กฐ ๋ช๋ น์ด ์ํ์ ๋ช๋ น์ด, ๋ฐ์ดํฐ ๋ฑ ์ฌ๋ฌ ํ์ด์ง๋ฅผ ๋์์ ์ฐธ์กฐํด์ผ ํ๋ ๊ฒฝ์ฐ๊ฐ ์๊ธธ ์ ์๋ค. ์ด๋ ๋ช๋ น์ด ์ํ์ ์ํด ์ต์ํ ํ ๋น๋์ด์ผ ํ๋ ํ๋ ์์ ์๊ฐ ์๋๋ฐ ์ด ๋์ `Loop`๋ฅผ ๊ตฌ์ฑํ๋ ํ์ด์ง๋ค์ ํ๊บผ๋ฒ์ `allocate`๋๋ ๊ฒ์ด ์ ๋ฆฌํ๋ค. ๋ง์ฝ ํ์ง ์์ผ๋ฉด ๋งค `Loop`๋ง๋ค `page fault`๊ฐ ์๊ฒจ ๋งค๋ฒ ๋์คํฌ์ ํ์ด์ง๋ฅผ ์ฐพ์ผ๋ฌ ๊ฐ์ผ ํ๊ธฐ ๋๋ฌธ์ ๋นํจ์จ์ ์ด๊ณ  ์ฑ๋ฅ์ด ๋จ์ด์ง ๊ฒ์ด๋ค.

## Allocation Scheme
* `Equal allocation` : ๋ชจ๋  ํ๋ก์ธ์ค์ ๋๊ฐ์ ๊ฐ์ ํ ๋น
* `Proportional allocation` : ํ๋ก์ธ์ค ํฌ๊ธฐ์ ๋น๋กํ์ฌ ํ ๋น
* `Priority allocation` : ํ๋ก์ธ์ค์ ์ฐ์ ์์์ ๋ฐ๋ผ ๋ค๋ฅด๊ฒ ํ ๋น

## Global replacement
* ํ๋ ์์ ์ป๊ธฐ ์ํด ๊ฒฝ์ํ๋ ํํ๋ผ ํ  ์ ์๋ค.
* `replace`์ ๋ค๋ฅธ ํ๋ก์ธ์ค์ ํ ๋น๋ ํ๋ ์์ ๋นผ์์ ์ฌ ์ ์๋ค.
* `FIFO`, `LRU`, `LFU` ๋ฑ์ ์๊ณ ๋ฆฌ์ฆ์ `global replacement`๋ก ์ฌ์ฉ์์ ํด๋น
* `Working set`, `PFF` ์๊ณ ๋ฆฌ์ฆ ์ฌ์ฉ

## Local replacement
* ๋ฏธ๋ฆฌ ํ ๋นํด ๋๋ ํํ์ด๋ค.
* ์์ ์๊ฒ ํ ๋น๋ ํ๋ ์ ๋ด์์๋ง ๊ต์ฒด
* `FIFO`, `LRU`, `LFU` ๋ฑ์ ์๊ณ ๋ฆฌ์ฆ์ ํ๋ก์ธ์ค๋ณ๋ก ์ด์์ ํด๋น <br><br>

# Thrashing
* ํ๋ก์ธ์ค์ ์ํํ ์ํ์ ํ์ํ ์ต์ํ์ `page frame`์๋ฅผ ํ ๋น ๋ฐ์ง ๋ชปํ ๊ฒฝ์ฐ ๋ฐ์
* `page fault rate`์ด ๋งค์ฐ ๋์์ง๋ค.
* `CPU` ์ฌ์ฉ๋ฅ ์ด ๋ฎ์์ง
* `OS`๋ `MPD (Multiprogramming degree)`๋ฅผ ๋์ฌ์ผ ํ๋ค๊ณ  ํ๋จ
* ๋ ๋ค๋ฅธ ํ๋ก์ธ์ค๊ฐ ์์คํ์ ์ถ๊ฐ๋จ(higher MPD)
* ํ๋ก์ธ์ค ๋น ํ ๋น๋ ํ๋ ์ ์๊ฐ ๋์ฑ ๊ฐ์๋๋ค.
* ํ๋ก์ธ์ค๋ ํ์ด์ง์ `swap in / swap out`์ผ๋ก ๋ฐ์๋ค. ๊ทธ๋์ ๋ฉ์ถฐ ์๋ ๊ฒ์ฒ๋ผ ๋ณด์ธ๋ค. (์ ์์ ์ธ ๋์ ๋ถ๊ฐ)
* ๊ทธ๋์ ๋๋ถ๋ถ์ ์๊ฐ์ `CPU`๋ ํ๊ฐํ๋ค.(log throughput)
* ๋์์ ๋ฉ๋ชจ๋ฆฌ์ ์ฌ๋ผ๊ฐ ์๋ ํ๋ก๊ทธ๋จ์ ์ ์กฐ์ ์ด ํ์ํ๋ค. ์ด๋ฅผ ์ํด ์ฌ์ฉ๋๋ ๊ฒ์ด `Working-set model`์ด๋ค.

## Working-Set Model
* ํ๋ก์ธ์ค๋ ํน์  ์๊ฐ ๋์ ์ผ์  ์ฅ์๋ง์ ์ง์ค์ ์ผ๋ก ์ฐธ์กฐํ๋ค.
* ์ง์ค์ ์ผ๋ก ์ฐธ์กฐ๋๋ ํด๋น ํ์ด์ง๋ค์ ์งํฉ์ `localityy set`์ด๋ผ ํ๋ค.
* `locality`์ ๊ธฐ๋ฐํ์ฌ ํ๋ก์ธ์ค๊ฐ ์ผ์  ์๊ฐ ๋์ ์ํํ๊ฒ ์ํ๋๊ธฐ ์ํด ํ๊บผ๋ฒ์ ๋ฉ๋ชจ๋ฆฌ์ ์ฌ๋ผ์ ์์ด์ผ ํ๋ ํ์ด์ง๋ค์ ์งํฉ์ `Working Set`์ด๋ผ ์ ์ํ๋ค.
* `Working Set` ๋ชจ๋ธ์์๋ ํ๋ก์ธ์ค์ `Working Set` ์ ์ฒด๊ฐ ๋ฉ๋ชจ๋ฆฌ์ ์ฌ๋ผ์ ์์ด์ผ ์ํ๋๊ณ  ๊ทธ๋ ์ง ์๊ณ  ๋ถ๋ถ์ ์ผ๋ก ์์ผ๋ฉด ๊ฐ์ง๊ณ  ์๋ ํ๋ ์๋ ๋ชจ๋ ๋ฐ๋ฉ ํ `swap out(suspend)`๋๋ค. ์ด๋ฅผ ํตํด `thrashing`์ ์ด๋ ์ ๋ ๋ฐฉ์งํ  ์ ์๋ค.
* `Working Set`์ ์ ๋๋ก ํ์งํ๊ธฐ ์ํด์๋ `window size`๋ฅผ ์ ๊ฒฐ์ ํด์ผ ํ๋ค. ๋๋ฌด ์์ผ๋ฉด `localiry set`์ ๋ชจ๋ ์์ฉํ์ง ๋ชปํ  ์ ์๊ณ , ๋๋ฌด ํฌ๋ฉด ์ฌ๋ฌ ๊ท๋ชจ์ `locality set`์ ์์ฉํ๊ฒ ๋  ๊ฒ์ด๋ค. 

## PFF (Page-Fault Frequency) Scheme
* `page fault rate`์ ์ํ๊ฐ๊ณผ ํํ๊ฐ์ ๋๋ค.
* `page fault`๊ฐ ๋ง์์๋ก ๋ง์ ํ์ด์ง ํ๋ ์์ ํ ๋นํ๊ณ  ๊ทธ๋ ์ง ์์ผ๋ฉด ํ ๋น๋ ํ๋ ์ ์๋ฅผ ์ค์ธ๋ค.
* ๋น ํ๋ ์์ด ์์ผ๋ฉด ์ผ๋ถ ํ๋ก์ธ์ค๋ฅผ `swap out` ์ํจ๋ค.<br><br>

# Page Size์ ๊ฒฐ์ 
* ํ์ด์ง์ ํฌ๊ธฐ๊ฐ ๋๋ฌด ์์ผ๋ฉด ํ์ด์ง ์๊ฐ ์ฆ๊ฐํ๊ฒ ๋์ด ํ์ด์ง ํ์ด๋ธ์ ํฌ๊ธฐ๊ฐ ์ฆ๊ฐํ  ๊ฒ์ด๋ค. 
* ์๊ฒ ์ชผ๊ฐ์ง๋งํผ ๋๋ฌด ํ์ํ ์ ๋ณด๋ง ๋ฉ๋ชจ๋ฆฌ์ ์ฌ๋ผ์ ์๊ฒ ๋์ด `page fault`์ ๋น๋์๊ฐ ๋์ด๋ ๋์คํฌ์ ์ ๊ทผํด์ผ ํ๋ ์๊ฐ์ด ๋์ด๋  ๊ฒ์ด๋ค. ์ด๋ฐ ์ธก๋ฉด์์ ๋ณผ ๋ ๋ฉ๋ชจ๋ฆฌ ์ด์ฉ์ ํจ์จ์ ์ด์ง๋ง `locality` ํ์ฉ ์ธก๋ฉด์์๋ ์ข์ง ์๋ค.<br><br><br>

# ์ถ์ฒ
* [์ด์์ฒด์  - ์ดํ์ฌ์๋ํ๊ต KOCW ๊ณต๊ฐ๊ฐ์](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
* [์ค๋ ์ฑ(thrashing)์ด๋ ๋ฌด์์ธ๊ฐ](https://straw961030.tistory.com/155)
