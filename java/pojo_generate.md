```groovy
import com.intellij.database.model.DasTable
import com.intellij.database.util.Case
import com.intellij.database.util.DasUtil
/*
* Available context bindings:
*   SELECTION   Iterable<DasObject>
*   PROJECT     project
*   FILES       files helper
*/
packageName = "com.sample;"
typeMapping = [
        (~/(?i)int/)                      : "long",
        (~/(?i)float|double|decimal|real/): "double",
        (~/(?i)datetime|timestamp/)       : "java.sql.Timestamp",
        (~/(?i)date/)                     : "java.sql.Date",
        (~/(?i)time/)                     : "java.sql.Time",
        (~/(?i)number/)                    : "Long",
        (~/(?i)/)                         : "String"
]
FILES.chooseDirectoryAndSave("Choose directory", "Choose where to store generated files") { dir ->
    SELECTION.filter { it instanceof DasTable }.each { generate(it, dir) }
}
def generate(table, dir) {
    def className = javaName(table.getName(), true)
    def fields = calcFields(table)
    new File(dir, className + "Entity.java").withPrintWriter { out -> generate(out, className, fields, table) }
}
def generate(out, className, fields, table) {
    def tableName = table.getName();
    out.println "package $packageName"
    out.println ""
    out.println "import lombok.Setter;"
    out.println "import lombok.Getter;"
    out.println "import java.io.Serializable;"
    out.println "import java.math.BigDecimal;"
    out.println "import java.util.Date;"
    out.println "import java.util.List;"
    out.println "import javax.persistence.*;"
    out.println "import javax.validation.constraints.*;"
    out.println "import org.hibernate.annotations.GenericGenerator;"
    out.println ""
    out.println "@Setter"
    out.println "@Getter"
    out.println "@Entity"
    out.println "@Table(name=\"${tableName}\")"
    out.println "public class ${underscoreToCamelCase(className)}Entity implements Serializable {"
    out.println ""
    out.println "  private static final long serialVersionUID = 1L;"
    out.println ""
    def primaryKeyField
    fields.each() {
//        out.println "  /** ${isNotEmpty(it.comment)} */"
        if (it.annos != "") out.println "  ${it.annos}"
        if (it.primary) {
            primaryKeyField = underscoreToCamelCase(it.name)
            out.println "  @Id"
            out.println "  @Basic(optional = false)"
            out.println "  @GeneratedValue(generator = \"generator\")"
            out.println "  @GenericGenerator(name = \"generator\", strategy = \"uuid.hex\")"
        }
        if (it.nullable && !it.foreign) out.println "  @NotNull"
        if (it.foreign) {
            out.println "  // @JoinColumn(name = \"${it.colName}\", referencedColumnName = \"${it.colName}\")"
            out.println "  // @ManyToOne(fetch = FetchType.LAZY)"
            it.foreignKeys.each() { fk ->
                if (DasUtil.containsName(it.colName, fk.getColumnsRef())) {
                    out.println "  // private ${underscoreToCamelCase(javaName(fk.getRefTableName(), true))}Entity ${underscoreToCamelCase(it.name)};"
                }
            }
        }
//      else {
        out.println "  @Column(name = \"${it.colName}\")"
        if (it.type.equals("String")) out.println "  @Size(max = ${it.size})"
        if (it.type.equals("Date")) out.println "  @Temporal(TemporalType.TIMESTAMP)"
        out.println "  private ${it.type} ${underscoreToCamelCase(it.name)};"
//        }
        out.println ""
    }
    out.println "}"
}
def calcFields(table) {
    DasUtil.getColumns(table).reduce([]) { fields, col ->
        def spec = Case.LOWER.apply(col.getDataType().getSpecification())
        def typeStr = typeMapping.find { p, t -> p.matcher(spec).find() }.value
        def colName = (String) col.getName()
        def size = col.getDataType().getLength()
        def nullable = col.isNotNull()
        def foreign = DasUtil.isForeign(col)
        def comment = col.getComment()
        def foreignKeys = DasUtil.getForeignKeys(table)
        fields += [[
                           name       : javaName(col.getName(), false),
                           type       : typeStr,
                           annos      : "",
                           colName    : col.getName(),
                           size       : size,
                           primary    : DasUtil.isPrimary(col),
                           nullable   : nullable,
                           foreign    : foreign,
                           foreignKeys: foreignKeys,
                           comment: comment
                   ]]
    }
}
def javaName(str, capitalize) {
    def s = com.intellij.psi.codeStyle.NameUtil.splitNameIntoWords(str)
            .collect { Case.LOWER.apply(it).capitalize() }
            .join("")
            .replaceAll(/[^\p{javaJavaIdentifierPart}[_]]/, "_")
    capitalize || s.length() == 1? s : Case.LOWER.apply(s[0]) + s[1..-1]
}
String underscoreToCamelCase(String underscore) {
    if (!underscore || underscore.isAllWhitespace()) {
        return ''
    }
    return underscore.replaceAll(/_\w/) { it[1].toUpperCase() }
}
def isNotEmpty(content) {
    if(content == null) {
        return ""
    }
    return content
}



```

