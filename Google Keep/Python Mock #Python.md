---
tags:
  - Keep/Label/Learn
  - Keep/Label/Python
---

Python UnitTest - Mock -

unittest.mock é uma biblioteca para teste em Python. Que permite substituir partes do seu sistema em teste por objetos simulados e fazer afirmações sobre como elas foram usadas.

unittest.mock fornece uma classe core Mock removendo a necessidade de criar uma série de stubs em todo o seu conjunto de testes. Depois de executar uma ação, você pode fazer afirmações sobre quais métodos / atributos foram usados e com quais argumentos foram chamados. Você também pode especificar valores de retorno e definir os atributos necessários da maneira normal.

Adicionalmente, o mock fornece um decorador patch() que lida com os atributos do módulo de patch e do nível de classe no escopo de um teste, junto com sentinel para criar objetos únicos. Veja o guia rápido para alguns exemplos de como usar Mock, MagicMock e patch().

Mock is designed for use with unittest and is based on the ‘action -> assertion’ pattern instead of ‘record -> replay’ used by many mocking frameworks.

Existe um backport de unittest.mock para versões anteriores do Python, disponível como mock no PyPI.

• https://docs.python.org/pt-br/3/library/unittest.mock.html#module-unittestmock
• https://realpython.com/python-mock-library/
• https://www.youtube.com/watch?v=wJb1ACohyOM

 
