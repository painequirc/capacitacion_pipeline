#!groovy

node {
   // ------------------------------------
   // -- ETAPA: Compilar
   // ------------------------------------
   stage ('Compilar')
   
   // -- Configura variables
   echo 'Configurando variables'
   echo "var env.mvn='${env.mvn}'"
   var env.mvn='${env.mvn}'
   env.PATH = "${env.mvn}:${env.PATH}"
   
   echo "var env.PATH='${env.mvn}'"
   
   // -- Descarga código desde SCM
   echo 'Descargando codigo de SCM'
   
   checkout scm
   
   // -- Compilando
   echo 'Compilando aplicacion'
   bat 'mvn clean compile'
   
   
   
   // ------------------------------------
   // -- ETAPA: Test
   // ------------------------------------
   stage ('Test')
   echo 'Ejecutando tests'
   try{
      bat 'mvn verify'
      step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
   }catch(err) {
      step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
      if (currentBuild.result == 'UNSTABLE')
         currentBuild.result = 'FAILURE'
      throw err
   }
   
   // ------------------------------------
   // -- ETAPA: Instalar
   // ------------------------------------
   stage ('Instalar')
   echo 'Instala el paquete generado en el repositorio maven'
   bat 'mvn install -Dmaven.test.skip=true'
   
   // ------------------------------------
   // -- ETAPA: Archivar
   // ------------------------------------
   stage ('Archivar')
   echo 'Archiva el paquete el paquete generado en Jenkins'
   step([$class: 'ArtifactArchiver', artifacts: '**/target/*.jar, **/target/*.war', fingerprint: true])
}
