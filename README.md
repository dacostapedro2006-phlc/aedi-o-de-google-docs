#editor-de-google-docs
# 📑 Otimizador de Documentos Google Docs

Este projeto é um **script em Google Apps Script** criado para facilitar a **edição e padronização rápida de livros digitalizados e convertidos em Google Docs**.
Ele automatiza tarefas repetitivas de formatação, limpeza e organização, deixando o documento pronto para revisão, leitura ou publicação.

---

## 🎯 Objetivo do Projeto

Muitas vezes, ao digitalizar livros ou materiais e convertê-los em texto dentro do Google Docs, o resultado vem com problemas como:

* Espaços em branco desnecessários
* Quebras de página irregulares
* Padrões de título desconfigurados
* Páginas numeradas misturadas ao texto
* Dificuldade em gerar um sumário funcional

👉 Este script resolve esses pontos em poucos cliques, adicionando um menu personalizado dentro do Google Docs.

---

## ⚙️ Funcionalidades

O menu **📑 Otimizar Documento** disponibiliza 6 etapas principais:

1. **Configurar Página**

   * Define o tamanho da página como **A4**
   * Ajusta as margens para valores padronizados (2 cm em cima/baixo e 1.5 cm nas laterais)

2. **Padronizar Estilos**

   * Define fonte padrão como **Nunito**
   * Aplica tamanhos, cores e negrito de forma hierárquica em **Títulos (1 a 6)**
   * Ajusta espaçamento entre linhas (1.15) e parágrafos (antes = 12, depois = 6)

3. **Organizar Páginas**

   * Detecta cabeçalhos no formato **"PÁGINA N"**
   * Converte para **Título 5** padronizado
   * Insere quebra de página antes de cada marcação

4. **Limpar Espaços Extras**

   * Remove parágrafos vazios redundantes
   * Elimina quebras de página duplicadas
   * Mantém apenas separações realmente necessárias

5. **Finalizar Documento**

   * Remove espaços, linhas e quebras de página no final do documento
   * Garante que o texto termine corretamente, sem resíduos desnecessários

6. **Gerar Sumário Manual**

   * Cria um sumário em tabela, listando apenas **Títulos 2**
   * Associa cada entrada ao número da página mais próximo da marcação "PÁGINA N"
   * Adiciona preenchimento com **pontilhado** estilo tradicional de livros impressos

---

## 🛠️ Estrutura do Código

* **onOpen()** → Cria o menu personalizado dentro do Google Docs
* **configurarPagina()** → Ajusta margens e formato da página
* **padronizarEstilos()** → Uniformiza fontes, cores e espaçamentos
* **organizarPaginas()** → Reestrutura quebras e numeração de páginas
* **limparEspacosExtras()** → Remove elementos redundantes
* **finalizarDocumento()** → Faz a limpeza final
* **gerarSumarioManual()** → Cria um sumário customizado com pontilhado e número de páginas
* **isWhitespaceOnly()** → Função auxiliar para verificar se um parágrafo contém apenas espaços

---

## 🚀 Como Usar

1. Abra seu documento no Google Docs.
2. Acesse **Extensões > Apps Script**.
3. Cole o código no editor e salve.
4. Recarregue o Google Docs.
5. Um novo menu chamado **📑 Otimizar Documento** aparecerá.
6. Use as opções na ordem (1 a 6) para transformar o documento.

---

## 📌 Observações Importantes

* A função **configurarPagina()** utiliza a **Google Docs API** (e não apenas DocumentApp).
  👉 Portanto, é necessário ativar a API **Google Docs** no projeto do Apps Script.
* O sumário é **manual**: não cria links clicáveis, mas organiza os títulos em uma tabela com pontilhado e número da página.
* O script foi pensado para livros ou apostilas escaneados e convertidos, mas pode ser adaptado para outros tipos de documentos.

---

## 🗂️ Possíveis Melhorias Futuras

* Adicionar opção de exportar o documento diretamente para **PDF otimizado**.
* Permitir escolher quais títulos (H2, H3, H4...) entram no sumário.
* Criar um **modo revisão**, destacando o que foi modificado.
* Melhorar a detecção de "PÁGINA N" para diferentes padrões de OCR.

---

## 📜 Licença

Este projeto é open-source e pode ser usado livremente.
Sinta-se à vontade para melhorar, adaptar e contribuir!

    
    /**
     * 📑 OTIMIZADOR COMPLETO DE DOCUMENTOS GOOGLE DOCS
     * 
     * - Configura página A4 com margens corretas
     * - Padroniza Texto Normal e Títulos (1-6) com cores/tamanhos certos
     * - Ajusta espaçamento: linhas 1.15, Antes=12, Depois=6
     * - Garante "PÁGINA N" como Título 5, com quebra antes
     * - Remove páginas vazias, espaços extras, quebras duplicadas
     * - Gera sumário manual sem incluir "PÁGINA N"
     * 
     * ⚠️ Precisa ativar a Google Docs API para configurar margens.
     */
    
    function onOpen() {
      DocumentApp.getUi()
        .createMenu('📑 Otimizar Documento')
        .addItem('1️⃣ Configurar Página', 'configurarPagina')
        .addItem('2️⃣ Padronizar Estilos', 'padronizarEstilos')
        .addItem('3️⃣ Organizar Páginas', 'organizarPaginas')
        .addItem('4️⃣ Limpar Espaços Extras', 'limparEspacosExtras')
        .addItem('5️⃣ Finalizar Documento', 'finalizarDocumento')
        .addItem('6️⃣ Gerar Sumário', 'gerarSumarioManual')
        .addToUi();
    }
    
    /** ===================== ETAPA 1 ===================== */
    function configurarPagina() {
      const doc = DocumentApp.getActiveDocument();
      const docId = doc.getId();
    
      const requests = [{
        updateDocumentStyle: {
          documentStyle: {
            pageSize: {
              width: { magnitude: 595.28, unit: 'PT' },  // A4
              height: { magnitude: 841.89, unit: 'PT' }
            },
            marginTop: { magnitude: 56.69, unit: 'PT' },   // 2 cm
            marginBottom: { magnitude: 56.69, unit: 'PT' },// 2 cm
            marginLeft: { magnitude: 42.52, unit: 'PT' },  // 1.5 cm
            marginRight: { magnitude: 42.52, unit: 'PT' }  // 1.5 cm
          },
          fields: 'pageSize,marginTop,marginBottom,marginLeft,marginRight'
        }
      }];
    
      Docs.Documents.batchUpdate({ requests }, docId);
      DocumentApp.getUi().alert("✅ Página configurada!");
    }
    
    /** ===================== ETAPA 2 ===================== */
    function padronizarEstilos() {
      const body = DocumentApp.getActiveDocument().getBody();
      const paragraphs = body.getParagraphs();
    
      paragraphs.forEach(p => {
        const heading = p.getHeading();
        let style = {};
    
        switch (heading) {
          case DocumentApp.ParagraphHeading.HEADING1:
            style = { fontSize: 20, bold: true, color: '#000000' }; break;
          case DocumentApp.ParagraphHeading.HEADING2:
            style = { fontSize: 18, bold: true, color: '#666666' }; break; // Cinza escuro 4
          case DocumentApp.ParagraphHeading.HEADING3:
            style = { fontSize: 16, bold: true, color: '#434343' }; break; // Cinza escuro 3
          case DocumentApp.ParagraphHeading.HEADING4:
            style = { fontSize: 14, bold: true, color: '#434343' }; break;
          case DocumentApp.ParagraphHeading.HEADING5:
            style = { fontSize: 12, bold: false, color: '#434343' }; break;
          case DocumentApp.ParagraphHeading.HEADING6:
            style = { fontSize: 11, bold: false, color: '#434343' }; break;
          default:
            style = { fontSize: 11, bold: false, color: '#000000' };
        }
    
        p.setFontFamily('Nunito');
        p.setFontSize(style.fontSize);
        p.setForegroundColor(style.color);
        p.setBold(style.bold);
    
        // Espaçamento
        p.setLineSpacing(1.15);
        p.setSpacingBefore(12);
        p.setSpacingAfter(6);
      });
    
      DocumentApp.getUi().alert("✅ Estilos e espaçamento padronizados!");
    }
    
    /** ===================== ETAPA 3 ===================== */
    function organizarPaginas() {
      const body = DocumentApp.getActiveDocument().getBody();
      for (let i = 0; i < body.getNumChildren(); i++) {
        const el = body.getChild(i);
        if (el.getType() !== DocumentApp.ElementType.PARAGRAPH) continue;
    
        const p = el.asParagraph();
        const text = (p.getText() || '').trim();
        if (!/^P[\u00C1A]GINA\s*\d+$/.test(text.toUpperCase())) continue;
    
        p.setHeading(DocumentApp.ParagraphHeading.HEADING5);
        p.setFontFamily('Nunito');
        p.setFontSize(12);
        p.setForegroundColor('#434343');
        p.setBold(false);
    
        // Remove espaços acima
        while (i > 0) {
          const prev = body.getChild(i - 1);
          if (prev.getType() === DocumentApp.ElementType.PARAGRAPH &&
              isWhitespaceOnly(prev.asParagraph().getText())) {
            prev.removeFromParent();
            i--;
            continue;
          }
          break;
        }
    
        // Insere quebra de página antes
        if (i > 0 && body.getChild(i - 1).getType() !== DocumentApp.ElementType.PAGE_BREAK) {
          body.insertPageBreak(i);
          i++;
        }
      }
      DocumentApp.getUi().alert("✅ Páginas organizadas!");
    }
    
    /** ===================== ETAPA 4 ===================== */
    function limparEspacosExtras() {
      const body = DocumentApp.getActiveDocument().getBody();
      let i = 0;
    
      while (i < body.getNumChildren()) {
        const el = body.getChild(i);
    
        // Remove parágrafos completamente vazios
        if (el.getType() === DocumentApp.ElementType.PARAGRAPH &&
            isWhitespaceOnly(el.asParagraph().getText())) {
    
          const prev = i > 0 ? body.getChild(i - 1) : null;
          const next = i + 1 < body.getNumChildren() ? body.getChild(i + 1) : null;
    
          // Se estiver entre dois parágrafos de texto → remove
          if (prev && next &&
              prev.getType() === DocumentApp.ElementType.PARAGRAPH &&
              !isWhitespaceOnly(prev.asParagraph().getText()) &&
              next.getType() === DocumentApp.ElementType.PARAGRAPH &&
              !isWhitespaceOnly(next.asParagraph().getText())) {
            el.removeFromParent();
            continue;
          }
    
          // Se for vazio no começo ou no fim → remove também
          if (body.getNumChildren() > 1) {
            el.removeFromParent();
            continue;
          }
        }
    
        // Remove quebras duplicadas
        if (el.getType() === DocumentApp.ElementType.PAGE_BREAK &&
            i + 1 < body.getNumChildren() &&
            body.getChild(i + 1).getType() === DocumentApp.ElementType.PAGE_BREAK) {
          el.removeFromParent();
          continue;
        }
    
        i++;
      }
    
      DocumentApp.getUi().alert("✅ Espaços extras entre textos removidos!");
    }
    
    /** ===================== ETAPA 5 ===================== */
    function finalizarDocumento() {
      const body = DocumentApp.getActiveDocument().getBody();
      while (body.getNumChildren() > 0) {
        const last = body.getChild(body.getNumChildren() - 1);
        if (last.getType() === DocumentApp.ElementType.PARAGRAPH &&
            isWhitespaceOnly(last.asParagraph().getText())) {
          if (body.getNumChildren() === 1) {
            last.asParagraph().setText('');
            break;
          }
          last.removeFromParent();
        } else if (last.getType() === DocumentApp.ElementType.PAGE_BREAK) {
          last.removeFromParent();
        } else {
          break;
        }
      }
      DocumentApp.getUi().alert("✅ Documento finalizado!");
    }
    
    /** ===================== ETAPA 6 ===================== */
    /**
     * 📑 Gera sumário manual com pontilhado e número da página
     *    - Pega número da página baseado no último "PÁGINA N" (Título 5)
     *    - Adiciona pontilhado estilo "...................."
     */
    function gerarSumarioManual() {
      const doc = DocumentApp.getActiveDocument();
      const body = doc.getBody();
    
      // Remove sumário antigo
      const search = body.findText('SUMÁRIO');
      if (search) {
        const start = search.getElement().getParent();
        const idx = body.getChildIndex(start);
        body.removeChild(start);
        if (body.getNumChildren() > idx) {
          const next = body.getChild(idx);
          if (next.getType() === DocumentApp.ElementType.TABLE) {
            body.removeChild(next);
          }
        }
      }
    
      // Adiciona título do sumário
      const title = body.insertParagraph(0, 'SUMÁRIO');
      title.setHeading(DocumentApp.ParagraphHeading.HEADING1);
    
      // Preparar entradas
      const paragraphs = body.getParagraphs();
      const entries = [];
      let currentPage = 1;
    
      paragraphs.forEach(p => {
        const txt = p.getText();
        if (!txt) return;
    
        // Atualiza página se encontrar "PÁGINA N"
        if (/^P[\u00C1A]GINA\s*(\d+)$/i.test(txt)) {
          const match = txt.match(/^P[\u00C1A]GINA\s*(\d+)$/i);
          if (match) currentPage = parseInt(match[1], 10);
          return;
        }
    
        // Captura apenas Título 2
        if (p.getHeading() === DocumentApp.ParagraphHeading.HEADING2) {
          entries.push({ title: txt, page: currentPage });
        }
      });
    
      // Cria tabela estilo sumário
      const table = body.insertTable(1);
      table.setBorderWidth(0);
    
      entries.forEach(e => {
        const row = table.appendTableRow();
        const dotted = '.'.repeat(120); // Pontilhado longo
        row.appendTableCell(`${e.title} ${dotted} ${e.page}`);
      });
    
      DocumentApp.getUi().alert("✅ Sumário gerado com páginas!");
    }
    
    /** ===================== HELPERS ===================== */
    function isWhitespaceOnly(s) {
      return !s || s.replace(/[\s\u00A0]/g, '') === '';
    }
