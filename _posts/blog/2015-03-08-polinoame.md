---
layout: post
title: "Polinoame - o soluție minimă"
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
date: 2014-08-08T15:39:55-04:00
---

Tema nr. 1 avea ca scop să învățați câteva concepte OOP printr-un exercițiu legat de polinoame. Mai jos găsiți un cod minimal scris de mine în Python, care prezintă câteva astfel de concepte.

## Compoziția, moștenirea și încapsularea - prin patternul Composite
Ideea aici este să observăm că Monomul în sine este doar un caz particular de Polinom, astfel, având aceeași formă a constructorului și a metodelor de pe obiect, putem aplica logica folosită fără a fi nevoie să o copiem și adjustăm. Aceasta înseamnă că obiectele au singure cunoștiință despre ce trebuie să facă, iar noi pur și simplu le folosim. Pentru mai multe informații, verificați [patternul Composite][composite]

## Compoziția și delegarea - prin patternul Strategy
[Strategy][strategy] este un pattern care ne permite să delegăm rezolvarea unor probleme unor obiecte separate, pentru a păstra caracteristicile de unică responsabilitate ale unei clase. Aceasta înseamnă că o clasă `Polinome` păstrează doar informațiile legate de operațiile de construcție, adunare/scădere, dar nu și operațiile complexe pentru rezolvare (Se presupune că operațiile sunt mai complicate decât implementarea mea rudimentară).

## Erată
O greșeală a fost strecurată intenționat. Se oferă 0,5p. bonus pentru tema 1 studentului care descoperă primul această greșeală. (Valabil o dată / semigrupă, în data de 9 martie 2015.)

## Codul folosit
{% highlight python %}
class Polynomial:
	def __init__(self):
		self.components = {}

	def addMonomial(self, m):
		if self.components.get(m.exponent, False):
			self.components[m.exponent] += m.factor
		else:
			self.components[m.exponent] = m.factor

	def build(self, dictionary):
		self.components = dictionary
		if (self.getDegree() == 1):
			self.strategy = FirstOrderSolveStrategy()
		else:
			self.strategy = StupidSolveStrategy()

	def prettyPrint(self):
		result = ""
		for key, value in self.components.items():
			if value == 1:
				if key == 0:
					result += str(value) + '+'
				else:
					result += self.printExponent(key) + '+'
			elif value:
				result += str(value) + self.printExponent(key) + '+'
		
		return result

	def printExponent(self, key):
		if key == 0:
			return ''
		elif key == 1:
			return 'x'
		else:
			return 'x^' + str(key)

	def getDegree(self):
		l = sorted(self.components.keys(), reverse=True)
		return l[0]

	def solve(self):
		return self.strategy.solve(self)

	def add(self, p):
		result = Polynomial()
		components = {}

		for key, value in self.components.items():
			if key in p.components.keys():
				components[key] = value + p.components[key]
			else:
				components[key] = value

		for key2, value2 in p.components.items():
			if not key2 in self.components.keys():
				components[key2] = value2

		result.build(components)
		return result


class Monomial(Polynomial):
	def __init__(self):
		self.components = {}

	def build(self, dictionary):
		if len(dictionary) != 1:
			raise Exception("This is not a monomial, sry :(")

		self.exponent, self.factor = dictionary.popitem()
		self.components[self.exponent] = self.factor


class SolveStrategy:
	def solve(self, p):
		pass


class FirstOrderSolveStrategy(SolveStrategy):
	def solve(self, p):
		a = p.components[1]
		b = p.components.get(0, 0)
		return -b/a


class StupidSolveStrategy(SolveStrategy):
	def solve(self, p):
		return "I'm too stupid to solve higher order polynomials :("

{% endhighlight %}

[composite]: http://en.wikipedia.org/wiki/Composite_pattern
[strategy]:    http://en.wikipedia.org/wiki/Strategy_pattern
