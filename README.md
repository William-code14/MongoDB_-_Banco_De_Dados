# 🏆 Oscar MongoDB — Respostas Níveis 1 a 6

Base de dados com **11.104 registros** reais de indicações ao Oscar, de 1928 a 2026.

---

## Sumário

- [Nível 1 — Primeiros Passos](#nível-1--primeiros-passos)
- [Nível 2 — Explorando Categorias](#nível-2--explorando-categorias)
- [Nível 3 — Atores e Atrizes Famosos](#nível-3--atores-e-atrizes-famosos)
- [Nível 4 — Vencedores Históricos](#nível-4--vencedores-históricos)
- [Nível 5 — Análise de Indicações](#nível-5--análise-de-indicações)
- [Nível 6 — Análise de Filmes](#nível-6--análise-de-filmes)

---

## Nível 1 — Primeiros Passos

### 1.1 Quantos registros existem na coleção?

**R: 11.104 registros**

```js
db.PROA.countDocuments();
```

---

### 1.2 Quais são as categorias únicas?

**R: 122 categorias únicas**

```js
db.PROA.("categoria");
```

<details>
<summary>Ver todas as 122 categorias</summary>

`ACTOR`, `ACTOR IN A LEADING ROLE`, `ACTOR IN A SUPPORTING ROLE`, `ACTRESS`, `ACTRESS IN A LEADING ROLE`, `ACTRESS IN A SUPPORTING ROLE`, `ANIMATED FEATURE FILM`, `ANIMATED SHORT FILM`, `ART DIRECTION`, `ART DIRECTION (Black-and-White)`, `ART DIRECTION (Color)`, `ASSISTANT DIRECTOR`, `AWARD OF COMMENDATION`, `BEST MOTION PICTURE`, `BEST PICTURE`, `CASTING`, `CINEMATOGRAPHY`, `CINEMATOGRAPHY (Black-and-White)`, `CINEMATOGRAPHY (Color)`, `COSTUME DESIGN`, `COSTUME DESIGN (Black-and-White)`, `COSTUME DESIGN (Color)`, `DANCE DIRECTION`, `DIRECTING`, `DIRECTING (Comedy Picture)`, `DIRECTING (Dramatic Picture)`, `DOCUMENTARY`, `DOCUMENTARY (Feature)`, `DOCUMENTARY (Short Subject)`, `DOCUMENTARY FEATURE FILM`, `DOCUMENTARY SHORT FILM`, `ENGINEERING EFFECTS`, `FILM EDITING`, `FOREIGN LANGUAGE FILM`, `GORDON E. SAWYER AWARD`, `HONORARY AWARD`, `HONORARY FOREIGN LANGUAGE FILM AWARD`, `INTERNATIONAL FEATURE FILM`, `IRVING G. THALBERG MEMORIAL AWARD`, `JEAN HERSHOLT HUMANITARIAN AWARD`, `LIVE ACTION SHORT FILM`, `MAKEUP`, `MAKEUP AND HAIRSTYLING`, `MUSIC (Original Score)`, `MUSIC (Original Song)`, `PRODUCTION DESIGN`, `SHORT FILM (Live Action)`, `SHORT FILM (Animated)`, `SOUND`, `SOUND EDITING`, `SOUND MIXING`, `SOUND RECORDING`, `VISUAL EFFECTS`, `WRITING (Adapted Screenplay)`, `WRITING (Original Screenplay)` e muitas outras variações históricas.

</details>

---

### 1.3 Primeiro ano de cerimônia registrado

**R: 1928**

```js
db.PROA.find().sort({ ano_cerimonia: 1 }).limit(1);
```

---

### 1.4 Último ano de cerimônia registrado

**R: 2026**

```js
db.PROA.find().sort({ ano_cerimonia: -1 }).limit(1);
```

---

### 1.5 Quantas cerimônias estão registradas?

**R: 98 cerimônias**

```js
db.PROA.("cerimonia").length;
```

---

### 1.6 Atualização com dados do Oscar 2025 e 2026

Os dados de 2025 e 2026 já constam na base. Caso queira inserir registros manualmente, o modelo é:

```js
db.PROA.insertMany([
  {
    ano_filmagem: 2024,
    ano_cerimonia: 2025,
    cerimonia: 97,
    categoria: "BEST PICTURE",
    nome_do_indicado: "Anora",
    nome_do_filme: "Anora",
    vencedor: true
  }
]);
```

---

## Nível 2 — Explorando Categorias

### 2.1 Indicações por categoria

```js
db.PROA.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: -1 } }
]);
```

| Categoria | Total |
|-----------|------:|
| DIRECTING | 479 |
| FILM EDITING | 460 |
| ACTOR IN A SUPPORTING ROLE | 445 |
| ACTRESS IN A SUPPORTING ROLE | 445 |
| BEST PICTURE | 391 |
| DOCUMENTARY (Short Subject) | 378 |
| CINEMATOGRAPHY | 348 |
| DOCUMENTARY (Feature) | 345 |
| FOREIGN LANGUAGE FILM | 315 |
| ART DIRECTION | 307 |
| COSTUME DESIGN | 305 |
| MUSIC (Original Score) | 270 |
| SOUND | 255 |
| ACTOR IN A LEADING ROLE | 245 |
| ACTRESS IN A LEADING ROLE | 245 |
| ACTRESS | 236 |
| MUSIC (Original Song) | 235 |
| ACTOR | 232 |
| SHORT FILM (Live Action) | 226 |
| MUSIC (Song) | 215 |
| ... | ... |
| AWARD OF COMMENDATION | 1 |

---

### 2.2 Categoria com mais indicações

**R: DIRECTING — 479 indicações**

---

### 2.3 Categoria com menos indicações

**R: AWARD OF COMMENDATION — 1 indicação**

---

### 2.4 Último ano da categoria "ACTRESS"

**R: 1976** — a partir de 1977 passou a se chamar `ACTRESS IN A LEADING ROLE`.

```js
db.PROA
  .find({ categoria: "ACTRESS" })
  .sort({ ano_cerimonia: -1 })
  .limit(1);
```

---

### 2.5 Categorias de 1928 que não existem mais

**R: 12 categorias extintas**

| Categoria |
|-----------|
| ACTOR |
| ACTRESS |
| ART DIRECTION |
| DIRECTING (Comedy Picture) |
| DIRECTING (Dramatic Picture) |
| ENGINEERING EFFECTS |
| OUTSTANDING PICTURE |
| SPECIAL AWARD |
| UNIQUE AND ARTISTIC PICTURE |
| WRITING (Adaptation) |
| WRITING (Original Story) |
| WRITING (Title Writing) |

```js
const cats1928 = db.PROA.("categoria", { ano_cerimonia: 1928 });
const catsLast = db.PROA.("categoria", { ano_cerimonia: 2026 });
cats1928.filter(c => !catsLast.includes(c));
```

---

### 2.6 Categorias com a palavra "DIRECTING"

**R: 3 categorias**

1. `DIRECTING`
2. `DIRECTING (Comedy Picture)`
3. `DIRECTING (Dramatic Picture)`

```js
db.PROA.("categoria", {
  categoria: { $regex: "DIRECTING", $options: "i" }
});
```

---

## Nível 3 — Atores e Atrizes Famosos

### Natalie Portman

#### 3.1 Quantas vezes foi indicada?

**R: 3 indicações**

```js
db.PROA.countDocuments({ nome_do_indicado: "Natalie Portman" });
```

#### 3.2 Quantos Oscars ganhou?

**R: 1 Oscar**

```js
db.PROA.countDocuments({ nome_do_indicado: "Natalie Portman", vencedor: true });
```

#### 3.3 / 3.4 Anos, filmes e resultado

```js
db.PROA
  .find({ nome_do_indicado: "Natalie Portman" }, { ano_cerimonia: 1, categoria: 1, nome_do_filme: 1, vencedor: 1 })
  .sort({ ano_cerimonia: 1 });
```

| Ano | Categoria | Filme | Venceu? |
|-----|-----------|-------|:-------:|
| 2005 | ACTRESS IN A SUPPORTING ROLE | Closer | ❌ |
| 2011 | ACTRESS IN A LEADING ROLE | Black Swan | ✅ |
| 2017 | ACTRESS IN A LEADING ROLE | Jackie | ❌ |

---

### Viola Davis

#### 3.5 Quantas vezes foi indicada?

**R: 4 indicações**

#### 3.6 Quantos Oscars ganhou?

**R: 1 Oscar**

#### 3.7 Filmes

| Ano | Filme | Venceu? |
|-----|-------|:-------:|
| 2009 | Doubt | ❌ |
| 2012 | The Help | ❌ |
| 2017 | Fences | ✅ |
| 2021 | Ma Rainey's Black Bottom | ❌ |

---

### Amy Adams

#### 3.8 Amy Adams já ganhou algum Oscar?

**R: Não** — 6 indicações, zero vitórias.

```js
db.PROA.countDocuments({ nome_do_indicado: "Amy Adams", vencedor: true });
```

#### 3.9 Quantas vezes foi indicada sem ganhar?

**R: 6 vezes**

---

### Denzel Washington

#### 3.10 Já ganhou algum Oscar?

**R: Sim — 2 Oscars**

#### 3.11 Total de indicações

**R: 9 indicações**

#### 3.12 Oscars ganhos

```js
db.PROA.find(
  { nome_do_indicado: "Denzel Washington", vencedor: true },
  { ano_cerimonia: 1, categoria: 1, nome_do_filme: 1 }
);
```

| Ano | Categoria | Filme |
|-----|-----------|-------|
| 1990 | ACTOR IN A SUPPORTING ROLE | Glory |
| 2002 | ACTOR IN A LEADING ROLE | Training Day |

---

## Nível 4 — Vencedores Históricos

### 4.1 Primeiro Oscar de Melhor Atriz

**R: Janet Gaynor — 1928 — "7th Heaven"**

```js
db.PROA
  .find({ categoria: "ACTRESS", vencedor: true })
  .sort({ ano_cerimonia: 1 })
  .limit(1);
```

---

### 4.2 Primeiro Oscar de Melhor Ator

**R: Emil Jannings — 1928 — "The Last Command"**

```js
db.PROA
  .find({ categoria: "ACTOR", vencedor: true })
  .sort({ ano_cerimonia: 1 })
  .limit(1);
```

---

### 4.3 Total de vencedores na base

**R: 2.464 registros com `vencedor: true`**

```js
db.PROA.countDocuments({ vencedor: true });
```

---

### 4.4 Todos os vencedores de Melhor Filme

**R: 96 filmes** — distribuídos entre as categorias `OUTSTANDING PICTURE`, `OUTSTANDING PRODUCTION`, `OUTSTANDING MOTION PICTURE`, `BEST MOTION PICTURE` e `BEST PICTURE`.

```js
db.PROA.find({
  categoria: { $in: ["OUTSTANDING PICTURE", "OUTSTANDING PRODUCTION", "OUTSTANDING MOTION PICTURE", "BEST MOTION PICTURE", "BEST PICTURE"] },
  vencedor: true
}).sort({ ano_cerimonia: 1 });
```

<details>
<summary>Ver todos os 96 vencedores</summary>

| Ano | Filme |
|-----|-------|
| 1928 | Wings |
| 1929 | The Broadway Melody |
| 1930 | All Quiet on the Western Front |
| 1931 | Cimarron |
| 1932 | Grand Hotel |
| 1933 | Cavalcade |
| 1935 | It Happened One Night |
| 1936 | Mutiny on the Bounty |
| 1937 | The Great Ziegfeld |
| 1938 | The Life of Emile Zola |
| 1939 | You Can't Take It with You |
| 1940 | Gone with the Wind |
| 1941 | Rebecca |
| 1942 | How Green Was My Valley |
| 1943 | Mrs. Miniver |
| 1944 | Casablanca |
| 1945 | Going My Way |
| 1946 | The Lost Weekend |
| 1947 | The Best Years of Our Lives |
| 1948 | Gentleman's Agreement |
| 1949 | Hamlet |
| 1950 | All the King's Men |
| 1951 | All about Eve |
| 1952 | An American in Paris |
| 1953 | The Greatest Show on Earth |
| 1954 | From Here to Eternity |
| 1955 | On the Waterfront |
| 1956 | Marty |
| 1957 | Around the World in 80 Days |
| 1958 | The Bridge on the River Kwai |
| 1959 | Gigi |
| 1960 | Ben-Hur |
| 1961 | The Apartment |
| 1962 | West Side Story |
| 1963 | Lawrence of Arabia |
| 1964 | Tom Jones |
| 1965 | My Fair Lady |
| 1966 | The Sound of Music |
| 1967 | A Man for All Seasons |
| 1968 | In the Heat of the Night |
| 1969 | Oliver! |
| 1970 | Midnight Cowboy |
| 1971 | Patton |
| 1972 | The French Connection |
| 1973 | The Godfather |
| 1974 | The Sting |
| 1975 | The Godfather Part II |
| 1976 | One Flew over the Cuckoo's Nest |
| 1977 | Rocky |
| 1978 | Annie Hall |
| 1979 | The Deer Hunter |
| 1980 | Kramer vs. Kramer |
| 1981 | Ordinary People |
| 1982 | Chariots of Fire |
| 1983 | Gandhi |
| 1984 | Terms of Endearment |
| 1985 | Amadeus |
| 1986 | Out of Africa |
| 1987 | Platoon |
| 1988 | The Last Emperor |
| 1989 | Rain Man |
| 1990 | Driving Miss Daisy |
| 1991 | Dances With Wolves |
| 1992 | The Silence of the Lambs |
| 1993 | Unforgiven |
| 1994 | Schindler's List |
| 1995 | Forrest Gump |
| 1996 | Braveheart |
| 1997 | The English Patient |
| 1998 | Titanic |
| 1999 | Shakespeare in Love |
| 2000 | American Beauty |
| 2001 | Gladiator |
| 2002 | A Beautiful Mind |
| 2003 | Chicago |
| 2004 | The Lord of the Rings: The Return of the King |
| 2005 | Million Dollar Baby |
| 2006 | Crash |
| 2007 | The Departed |
| 2008 | No Country for Old Men |
| 2009 | Slumdog Millionaire |
| 2010 | The Hurt Locker |
| 2011 | The King's Speech |
| 2012 | The Artist |
| 2013 | Argo |
| 2014 | 12 Years a Slave |
| 2015 | Birdman or (The Unexpected Virtue of Ignorance) |
| 2016 | Spotlight |
| 2017 | Moonlight |
| 2018 | The Shape of Water |
| 2019 | Green Book |
| 2020 | Parasite |
| 2021 | Nomadland |
| 2022 | CODA |
| 2023 | Everything Everywhere All at Once |
| 2024 | Oppenheimer |

</details>

---

### 4.5 Quantos filmes diferentes já ganharam o Oscar?

**R: 1.329 filmes diferentes**

```js
db.PROA.aggregate([
  { $match: { vencedor: true } },
  { $group: { _id: "$nome_do_filme" } },
  { $count: "total_filmes" }
]);
```

---

## Nível 5 — Análise de Indicações

### 5.1 Indicados mais de uma vez

**R: 1.451 pessoas/entidades com mais de 1 indicação**

```js
db.PROA.aggregate([
  { $group: { _id: "$nome_do_indicado", total: { $sum: 1 } } },
  { $match: { total: { $gt: 1 } } },
  { $sort: { total: -1 } }
]);
```

> O campo `nome_do_indicado` inclui estúdios e países além de pessoas físicas. Entre atores e atrizes, **Meryl Streep** lidera com 21 indicações.

---

### 5.2 Maior número de indicações

**R: Metro-Goldwyn-Mayer com 64** (como estúdio produtor)

Entre compositores: **John Williams — 46 indicações**

Entre atores/atrizes: **Meryl Streep — 21 indicações**

---

### 5.3 Indicados mais de 3 vezes e nunca ganharam

**R: 118 no total** — Top 10:

| Nome | Indicações |
|------|----------:|
| Thomas Newman | 14 |
| George Folsey | 12 |
| Music and Lyric by Diane Warren | 11 |
| Randy Newman | 9 |
| Peter O'Toole | 8 |
| Robert Emmett Dolan | 8 |

```js
db.PROA.aggregate([
  {
    $group: {
      _id: "$nome_do_indicado",
      total: { $sum: 1 },
      vitorias: { $sum: { $cond: ["$vencedor", 1, 0] } }
    }
  },
  { $match: { total: { $gt: 3 }, vitorias: 0 } },
  { $sort: { total: -1 } }
]);
```

---

### 5.4 Artistas indicados em categorias diferentes

**R: 648 pessoas/entidades indicadas em mais de uma categoria distinta**

```js
db.PROA.aggregate([
  { $group: { _id: "$nome_do_indicado", categorias: { $addToSet: "$categoria" } } },
  { $match: { $expr: { $gt: [{ $size: "$categorias" }, 1] } } },
  { $sort: { _id: 1 } }
]);
```

---

### 5.5 Indicados com exatamente 1 indicação

**R: 5.731 pessoas/entidades**

---

### 5.6 Maior número de indicações em um único ano

**R: 1943 — 186 indicações**

```js
db.PROA.aggregate([
  { $group: { _id: "$ano_cerimonia", total: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 1 }
]);
```

---

## Nível 6 — Análise de Filmes

### 🎬 Toy Story

#### 6.1 Anos em que a franquia ganhou Oscar

**R: 2011 e 2020**

```js
db.PROA.find({
  nome_do_filme: { $regex: "Toy Story", $options: "i" },
  vencedor: true
}, { ano_cerimonia: 1, nome_do_filme: 1, categoria: 1 });
```

#### 6.2 Total de indicações da franquia

**R: 11 indicações**

#### 6.3 Categorias

| Categoria |
|-----------|
| ANIMATED FEATURE FILM |
| BEST PICTURE |
| MUSIC (Original Musical or Comedy Score) |
| MUSIC (Original Song) |
| SOUND EDITING |
| WRITING (Adapted Screenplay) |
| WRITING (Screenplay Written Directly for the Screen) |

---

### 🎬 Crash

#### 6.4 Edição em que concorreu

**R: 2006 (78ª cerimônia)**

#### 6.5 Total de indicações

**R: 6 indicações**

#### 6.6 Ganhou Melhor Filme?

**R: Sim** ✅ — Venceu em 3 categorias:

| Categoria |
|-----------|
| BEST PICTURE |
| FILM EDITING |
| WRITING (Original Screenplay) |

```js
db.PROA.find({ nome_do_filme: "Crash", vencedor: true });
```

---

### 🎬 Central do Brasil

#### 6.7 Aparece no banco?

**R: Sim** — cadastrado como "Central Station" (título internacional).

#### 6.8 Quantas indicações?

**R: 2 indicações**

| Ano | Categoria | Filme |
|-----|-----------|-------|
| 1999 | ACTRESS IN A LEADING ROLE | Central Station |
| 1999 | FOREIGN LANGUAGE FILM | Central Station |

```js
db.PROA.find({ nome_do_filme: { $regex: "Central Station", $options: "i" } });
```

---

## Resumo Executivo

| Métrica | Valor |
|---------|------:|
| Total de registros | 11.104 |
| Categorias únicas | 122 |
| Primeira cerimônia | 1928 |
| Última cerimônia | 2026 |
| Total de cerimônias | 98 |
| Total de vencedores | 2.464 |
| Filmes distintos premiados | 1.329 |
| Categoria mais frequente | DIRECTING (479) |
| Ano com mais indicações | 1943 (186) |
| Atriz mais indicada | Meryl Streep (21) |
