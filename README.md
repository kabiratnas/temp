# temp

# Make one column editable in HTML and save entries in browser localStorage
if (!is.null(editable_column) && editable_column %in% names(data)) {

  editable_target <- match(editable_column, names(data)) - 1L

  key_prefix <- if (is.null(storage_key)) {
    editable_column
  } else {
    storage_key
  }

  editable_js <- htmlwidgets::JS(sprintf(
    paste0(
      "function(td, cellData, rowData, row, col) {",
      "  var rowLabel = String(rowData[0] || '').trim();",

      "  if (rowLabel === 'Total') {",
      "    td.setAttribute('contenteditable', 'false');",
      "    return;",
      "  }",

      "  var key = '%s|' + rowLabel;",

      "  var saved = localStorage.getItem(key);",
      "  if (saved !== null) {",
      "    td.textContent = saved;",
      "  }",

      "  td.setAttribute('contenteditable', 'true');",
      "  td.setAttribute('spellcheck', 'true');",
      "  td.classList.add('editable-action-cell');",

      "  td.oninput = function() {",
      "    localStorage.setItem(key, td.innerText);",
      "  };",

      "}"
    ),
    key_prefix
  ))

  column_defs <- append(
    column_defs,
    list(
      list(
        targets = editable_target,
        createdCell = editable_js
      )
    )
  )
}

<style>

/* Editable Proposed action cells */
table.dataTable td.editable-action-cell {
  min-width: 180px;
  cursor: text;
  background-color: rgba(0, 0, 0, 0.025);
  outline: none;
}

table.dataTable td.editable-action-cell:focus {
  box-shadow: inset 0 0 0 2px rgba(0, 0, 0, 0.22);
}

table.dataTable td.editable-action-cell:empty::before {
  content: "Click to type";
  opacity: 0.45;
  font-style: italic;
}

</style>


editable_column = "Proposed action",
storage_key = "scorecard_q2_actions"

`Proposed action` = ""
