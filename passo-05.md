# Runas, passo 5: busca por palavras inteiras

A versão MVP1 do programa `runas` busca caracteres comparando uma substring do nome. Isso gera dois problemas: baixa precisão e baixa re

* Resultados demais: pesquisando "cat" vêm 82 caracteres, sendo que a maioria não tem nada a ver com gatos, por exemplo "MULTIPLICATION SIGN".
* Resultados de menos: a ordem das palavras na consulta deveria ser ignorada: "chess black" e "black chess" deveriam devolver os mesmos resultados, e "cat smiling" deveria encontrar todos estes caracteres:

```
U+1F638 😸 	GRINNING CAT FACE WITH SMILING EYES
U+1F63A 😺 	SMILING CAT FACE WITH OPEN MOUTH
U+1F63B 😻 	SMILING CAT FACE WITH HEART-SHAPED EYES
```

> __TEORIA__: na área de recuperação de informação (_information retrieval_) esses problemas são caracterizados por duas métricas: [precisão e revocação](https://pt.wikipedia.org/wiki/Precis%C3%A3o_e_revoca%C3%A7%C3%A3o) (_precision_, _recall_). Resultados demais é falta de precisão: o sistema está recuperando resultados irrelevantes, ou encontrando falsos positivos. Resultados de menos é falta de revocação: o sistema está deixando de recuperar resultados relevantes, ou seja, falsos negativos.

Vamos melhorar a precisão e a revocação pesquisando sempre por palavras inteiras. Poderíamos resolver o problea todo mexendo apenas na função `Listar`, mas isso deixaria ela muito grande e difícil de testar. Então vamos colocar um pouco das novas funcionalidades na função `AnalisarLinha` e em outras funções que criaremos aos poucos.

## Melhorias em `AnalisarLinha`

Em vez de devolver apenas o código e o nome do caractere, vamos fazer a função `AnalisarLinha` devolver também as palavras do nome, na forma de uma fatia de strings, ou seja, uma `[]string`.

Para começar, mudamos o teste `TestAnalisarLinha`:

```go
func TestAnalisarLinha(t *testing.T) {
	runa, nome, palavras := AnalisarLinha(linhaLetraA)
	if runa != 'A' {
		t.Errorf("Esperava 'A', veio %q", runa)
	}
	const nomeA = "LATIN CAPITAL LETTER A"
	if nome != nomeA {
		t.Errorf("Esperava %q, veio %q", nomeA, nome)
	}
  palavrasA := []string{"LATIN", "CAPITAL", "LETTER", "A"} // ➊
	if ! reflect.DeepEqual(palavras, palavrasA) { // ➋
		t.Errorf("\n\tEsperado: %q\n\trecebido: %q", palavrasA, palavras) // ➌
	}
}
```
