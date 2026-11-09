---
theme: seriph
title: '42 ≠ 42.0 ≠ "42"'
info: |
  42 ≠ 42.0 ≠ "42" — conformance across five SDKs is harder than you think.
  5-minute lightning talk, LearnKube Day Salt Lake City.
  Simon Schrottner — OpenFeature maintainer & TSC member, CNCF & AAIF Ambassador.
author: Simon Schrottner
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
transition: slide-left
mdc: true
fonts:
  sans: Inter
  mono: JetBrains Mono
addons:
  - slidev-addon-qrcode
routerMode: hash
layout: section
---

# 42 ≠ 42.0 ≠ "42"

## Conformance across five SDKs is harder than you think

<div class="pt-8 opacity-90 text-sm space-y-1">
  <div>Simon Schrottner · OpenFeature maintainer & TSC member · CNCF & AAIF Ambassador</div>
  <div><carbon:email class="inline"/> simon@schrottner.at &nbsp;·&nbsp;
       <carbon:link class="inline"/> <a href="https://schrottner.at" target="_blank">schrottner.at</a> &nbsp;·&nbsp;
       <carbon:logo-github class="inline"/> aepfli &nbsp;·&nbsp;
       <carbon:logo-linkedin class="inline"/> in/aepfli</div>
</div>

<div class="abs-br m-6 flex items-end gap-2">
  <a href="https://schrottner.at/42/" target="_blank" class="text-xs opacity-60 hover:opacity-100 text-right leading-tight pb-1 !text-inherit">
    <div>Slides — grab them now</div>
    <div class="font-mono text-[10px] opacity-80 mt-0.5">schrottner.at/42</div>
  </a>
  <div class="bg-white p-1 rounded dark:invert">
    <QRCode data="https://schrottner.at/42/" :width="100" :height="100" :margin="2" />
  </div>
</div>

<!--
5:00 total. Budget per slide is in each note. No "who am I" slide — the cover line does it.

Narrative rhythm (do not break):
  Hook (is 42 = 42?) → Flags in one breath → Same JSON, four languages
  → "42" as a string → Python punchline
  → It's a pipeline, not an app → What to do → Close

[0:00–0:15] "Hi, I'm Simon, I maintain OpenFeature. Five minutes, one number."
-->

---
layout: statement
---

<div class="text-7xl font-mono font-bold tracking-tight">
  42 <span class="opacity-40">=</span> 42<span v-click="1">.0</span>
</div>

<div v-click="2" class="mt-12 text-3xl">
  Depends on who's asking.
</div>

<!--
[0:15–0:40]
"Quick poll. Is 42 equal to 42?" — [let them say yes]
[click] "Is 42 equal to 42.0?" — [some yes, some hesitation]
[click] "Depends on who's asking. And with feature flags, a lot of people are asking."
-->

---
layout: statement
---

<div class="text-3xl leading-relaxed">

A feature flag is <span v-mark.highlight.yellow="1">a value your code reads at runtime</span> — so you can change behavior <span v-mark.highlight.yellow="2">without a redeploy</span>.

</div>

<div v-click="3" class="mt-14 flex items-center justify-center gap-6 text-lg">
  <div class="font-mono rounded border border-gray-300 px-4 py-2">max-retries: <b>42</b></div>
  <carbon:arrow-right class="text-2xl opacity-50"/>
  <div class="flex gap-5 text-4xl">
    <logos-go /> <logos-java /> <logos-python /> <logos-javascript />
  </div>
</div>

<div v-click="4" class="mt-6 text-xl opacity-80">One definition. Many readers.</div>

<style>
.slidev-layout p { line-height: 1.6 !important; }
</style>

<!--
[0:40–1:10]
"Feature flags in one breath: a value your code reads at runtime [click], so you change behavior without a redeploy [click]."
[click] "In any real org that one value is read by services in four, five languages."
[click] "One definition, many readers. And every reader has its own idea of what a number is."
-->

---

# Same JSON, four languages

<div class="font-mono text-center text-2xl my-6 opacity-80">{ "max-retries": 42.0 }</div>

<div class="grid grid-cols-4 gap-4 mt-4">
  <div v-click class="rounded-lg border border-gray-200 shadow-sm p-4 flex flex-col gap-3">
    <div class="flex items-center gap-2 font-bold"><logos-go class="text-2xl"/> Go</div>
    <code class="text-sm">float64(42)</code>
    <div class="text-sm opacity-70">Decoded into <code>any</code>, every JSON number is a float64.</div>
  </div>
  <div v-click class="rounded-lg border border-gray-200 shadow-sm p-4 flex flex-col gap-3">
    <div class="flex items-center gap-2 font-bold"><logos-javascript class="text-2xl"/> JavaScript</div>
    <code class="text-sm">42</code>
    <div class="text-sm opacity-70">There is no int. <code>42 === 42.0</code>, and the <code>.0</code> is simply gone.</div>
  </div>
  <div v-click class="rounded-lg border border-gray-200 shadow-sm p-4 flex flex-col gap-3">
    <div class="flex items-center gap-2 font-bold"><logos-java class="text-2xl"/> Java</div>
    <code class="text-sm">Double 42.0</code>
    <div class="text-sm opacity-70">Write <code>42</code> and you get an <code>Integer</code>. The spelling picks the type.</div>
  </div>
  <div v-click class="rounded-lg border border-gray-200 shadow-sm p-4 flex flex-col gap-3">
    <div class="flex items-center gap-2 font-bold"><logos-python class="text-2xl"/> Python</div>
    <code class="text-sm">float 42.0</code>
    <div class="text-sm opacity-70"><code>42 == 42.0</code> is <code>True</code>. Hold that thought.</div>
  </div>
</div>

<!--
[1:10–2:00]
"Same JSON document. Four parsers."
[click] Go: "Unmarshal into any — float64. Always. Even for 42."
[click] JS: "There is no integer. 42.0 comes back as 42, the information is gone."
[click] Java: "Jackson gives you a Double here — and an Integer if someone had typed 42. The spelling decides."
[click] Python: "float, but 42 == 42.0 is True. Hold that thought."
"Now ask each SDK: give me this flag as an integer. What should happen?"
-->

---

# And then there's `"42"`

<div class="grid grid-cols-2 gap-6 mt-10 text-lg">
  <div v-click class="rounded-lg border border-gray-200 shadow-sm p-5 flex flex-col gap-2">
    <div class="flex items-center gap-2 font-bold"><logos-javascript class="text-2xl"/> JavaScript</div>
    <code>"42" == 42</code><div class="text-sm opacity-70">→ <b>true</b>. Strings get coerced to numbers.</div>
  </div>
  <div v-click class="rounded-lg border border-gray-200 shadow-sm p-5 flex flex-col gap-2">
    <div class="flex items-center gap-2 font-bold"><logos-python class="text-2xl"/> Python</div>
    <code>"42" == 42</code><div class="text-sm opacity-70">→ <b>False</b>. No coercion, no error either.</div>
  </div>
  <div v-click class="rounded-lg border border-gray-200 shadow-sm p-5 flex flex-col gap-2">
    <div class="flex items-center gap-2 font-bold"><logos-java class="text-2xl"/> Java</div>
    <code>"42".equals(42)</code><div class="text-sm opacity-70">→ <b>false</b>. Different types, quietly unequal.</div>
  </div>
  <div v-click class="rounded-lg border border-gray-200 shadow-sm p-5 flex flex-col gap-2">
    <div class="flex items-center gap-2 font-bold"><logos-go class="text-2xl"/> Go</div>
    <code>"42" == 42</code><div class="text-sm opacity-70">→ <b>doesn't compile</b>. The only one that tells you.</div>
  </div>
</div>

<div v-click class="mt-8 text-xl text-center">Loud is annoying. <b>Silent is dangerous.</b></div>

<!--
[2:00–2:50]
"And the third one in the title: the string 42. Someone typed quotes in the config. Happens all the time."
[click] JS: "Equal. JavaScript helpfully converts."
[click] Python: "Not equal. No error either — your if just goes the other way."
[click] Java: "Also false, also silent."
[click] Go: "Won't even compile. The only language that complains."
[click] "A loud failure costs you an afternoon. A silent one ships."
-->

---
layout: statement
---

```python
>>> isinstance(True, int)
True
>>> True + True
2
```

<div v-click class="mt-10 text-2xl">Your boolean flag is a perfectly valid integer flag.</div>

<style>
.slidev-code { font-size: 2rem !important; line-height: 1.5 !important; text-align: left; display: inline-block; padding: 1.5rem 3rem !important; }
</style>

<!--
[2:50–3:10]
"In Python, bool is a subclass of int. True is 1. True plus True is 2."
[click] "So if your provider checks 'is this an int?', your boolean flag passes. Happily."
[beat — let the laugh land, then turn]
"So far this sounds like a language problem. It isn't."
-->

---

# It's not an app problem. It's a pipeline.

<div class="flex items-stretch gap-2 mt-10 text-sm">
  <div class="hop" style="flex:1.5">
    <div class="hop-title">YAML</div>
    <div v-click="1" class="hop-bug"><code>version: 1.10</code> → <code>1.1</code><br/><code>country: NO</code> → <code>false</code></div>
  </div>
  <carbon:arrow-right class="arrow"/>
  <div class="hop">
    <div class="hop-title">ConfigMap / flag service</div>
    <div v-click="2" class="hop-bug">stores whatever<br/>it parsed</div>
  </div>
  <carbon:arrow-right class="arrow"/>
  <div class="hop">
    <div class="hop-title">JSON on the wire</div>
    <div v-click="3" class="hop-bug">one number type —<br/><code>42.0</code> → <code>42</code></div>
  </div>
  <carbon:arrow-right class="arrow"/>
  <div class="hop">
    <div class="hop-title">Your service</div>
    <div v-click="4" class="hop-bug">asks for an int,<br/><code>0.5</code> → <code>0</code></div>
  </div>
</div>

<div v-click="5" class="mt-12 text-xl text-center">Every hop can re-type your flag. <b>The platform owns most of the hops.</b></div>

<style>
.hop { flex: 1; border: 1px solid rgba(128,128,128,.35); border-radius: .5rem; padding: .9rem .7rem; display: flex; flex-direction: column; gap: .7rem; min-height: 9rem; }
.hop-title { font-weight: 700; font-size: 1.2rem; }
.hop-bug code { white-space: nowrap; }
.hop-bug { font-size: 1rem; line-height: 1.5; }
.arrow { align-self: center; flex: none; font-size: 1.25rem; opacity: .4; }
</style>

<!--
[3:10–4:10]
"A flag value doesn't go from the author straight into your if-statement."
[click] "It starts in YAML. 1.10 is a float, so it's 1.1. And with an older YAML parser — hello, Norway — NO is false."
[click] "It gets stored in a ConfigMap or a flag service — exactly as it was parsed."
[click] "It goes over the wire as JSON. One number type. The .0 is gone."
[click] "And then your service asks for an integer and gets 0.5 rounded down to zero."
[click] "Every hop can re-type your flag. And who owns the YAML, the ConfigMaps, the flag service, the network? The platform team. This is your problem too."
-->

---

# So what do we do?

<v-clicks>

- **Pick the type at the source** — and keep it in the schema, not in people's heads
- **Quote what isn't a number** — `"1.10"`, `"NO"`
- **Read the same flag in every language you ship** — and test that they agree
- **Fail loud** — a type mismatch should be an error, not a fallback

</v-clicks>

<style>
.slidev-layout li { font-size: 1.5rem; line-height: 1.5; margin-top: .8rem; }
</style>

<div v-click class="mt-10 text-xl text-center">This is exactly why flag standards like OpenFeature test every SDK against the same behaviour.</div>

<!--
[4:10–4:45]
"So what do we do?"
[click] "Decide the type where the flag is defined — in a schema, not in someone's head."
[click] "Quote anything that only looks like a number."
[click] "If you ship four languages, read the same flag in all four and check they agree."
[click] "And fail loud. A wrong type should be an error, not a silent default."
[click] "That's the whole reason flag standards like OpenFeature test every SDK against the same behaviour — but you don't need a standard to start doing this tomorrow."
-->

---
layout: end
---

# Consistency is a feature.

<div class="flex items-center justify-center gap-16 mt-8">
  <div class="text-sm opacity-80 tracking-normal leading-relaxed text-left">
    <carbon:email class="inline"/> simon@schrottner.at<br/>
    <carbon:link class="inline"/> <a href="https://schrottner.at" target="_blank">schrottner.at</a><br/>
    <carbon:logo-github class="inline"/> aepfli &nbsp;·&nbsp; <carbon:logo-linkedin class="inline"/> in/aepfli
  </div>
  <a href="https://openfeature.dev" target="_blank" class="flex flex-col items-center gap-2 !text-inherit !border-0 tracking-normal">
    <div class="bg-white p-2 rounded-lg">
      <QRCode data="https://openfeature.dev" :width="150" :height="150" :margin="2" />
    </div>
    <div class="font-mono text-xs opacity-70">openfeature.dev</div>
  </a>
  <a href="https://schrottner.at/42/" target="_blank" class="flex flex-col items-center gap-2 !text-inherit !border-0 tracking-normal">
    <div class="bg-white p-2 rounded-lg">
      <QRCode data="https://schrottner.at/42/" :width="150" :height="150" :margin="2" />
    </div>
    <div class="font-mono text-xs opacity-70">slides</div>
  </a>
</div>

<!--
[4:45–5:00]
"42 is 42 only if everyone in the pipeline agrees on what 42 is. Consistency is a feature — test for it. Thanks!"
-->
