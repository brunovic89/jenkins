pipeline { 
    agent {label 'master'}
		parameters {
				string(name: 'URL',defaultValue: '', description: 'INSIRA A URL DA APLICAÇÃO EX: http://host:porta/contexto')
				string(name: 'PACOTE', defaultValue: '', description: 'INSIRA O NOME DA APLICÃO PARA DEPLOY')
				}
    stages { 
		stage('Deploy TOMCAT'){ 
		   steps{
		    echo "SCRIPT PARA DEPLOY DE APP EM TOMCAT"
            powershell '''
				$SERVICE_NAME = "Tomcat8"
				$SERVICE = Get-Service $SERVICE_NAME
				$TOMCAT_HOME = "C:\\Program Files\\Apache Software Foundation\\Tomcat 8.5"
				$APP_DIR = "$TOMCAT_HOME\\webapps"
				$BKP_DIR = "C:\\backupapp"
				$DEPLOY_APP = "C:\\newapp"
				$FILE = $env:PACOTE
				$URL = $env:URL
				$DATESTAMP = get-date -uformat "%Y%m%d@%H-%M-%S"
				$TESTE_PACOTE = Test-Path "$DEPLOY_APP\$FILE.war"
				$LOG = "$TOMCAT_HOME\\log_deploy.txt"
				$DATE = Get-Date -Format "dd/MM/yyyy - HH:mm:ss"
				
				
				if ($TESTE_PACOTE -eq $true) {
					
					$DATE = Get-Date -Format "dd/MM/yyyy - HH:mm:ss"
					Write-Host "INICIANDO PROCESSO DE DEPLOY"
					"$DATE - INICIANDO PROCESSO DE DEPLOY" >> $LOG | Out-Null
					Write-Host "PARANDO O TOMCAT"
					"$DATE - PARANDO O TOMCAT" >> $LOG | Out-Null
					Stop-Service -Name $SERVICE_NAME
					Start-Sleep 5
					$tentativa = 1
					while ($SERVICE.Status -ne "Stopped" -and $tentativa -le 3) {
						$DATE = Get-Date -Format "dd/MM/yyyy - HH:mm:ss"
						$SERVICE = Get-Service "Tomcat8"
						Write-Host "TENTANDO PARAR O SERVIÇO"
						"$DATE - TENTANDO PARAR O SERVIÇO" >> $LOG | Out-Null
						Start-Sleep 10
						$tentativa += 1
					}
				
					if ($tentativa -eq 4) {
						$DATE = Get-Date -Format "dd/MM/yyyy - HH:mm:ss"
						"$DATE - NÃO FOI POSSIVEL PARAR O TOMCAT" >> $LOG | Out-Null
						exit
					}
				
					else {
						$DATE = Get-Date -Format "dd/MM/yyyy - HH:mm:ss"
						"CONTINUANDO"
						"$DATE - CONTINUANDO" >> $LOG |Out-Null
					}
				
					$DATE = Get-Date -Format "dd/MM/yyyy - HH:mm:ss"
					$SERVICE = Get-Service $SERVICE_NAME
					$STATUS_SERVICE = $SERVICE.Status
					Write-Host "O SERVIÇO ESTA $STATUS_SERVICE" 
					"$DATE - O SERVIÇO ESTA $STATUS_SERVICE" >> $LOG | Out-Null
				
					Write-Host "FAZENDO O BACKUP DO PACOTE $FILE"
					"$DATE - FAZENDO O BACKUP DO PACOTE $FILE" >> $LOG | Out-Null
					Copy-Item "$APP_DIR\\$FILE.war" -Destination "$BKP_DIR\\$FILE.war_$DATESTAMP" 
					Remove-Item "$APP_DIR\\$FILE.war", "$APP_DIR\\$FILE" -Recurse
				
					Write-Host "COPIANDO NOVO PACOTE $FILE"
					"$DATE - COPIANDO NOVO PACOTE $FILE" >> $LOG | Out-Null
					Copy-Item "$DEPLOY_APP\\$FILE.war" -Destination "$APP_DIR"
				
					Write-Host "INICIANDO O TOMCAT"
					"$DATE - INICIANDO O TOMCAT" >> $LOG | Out-Null
					Start-Service -Name $SERVICE_NAME
					Start-Sleep 15
					
					$SERVICE = Get-Service $SERVICE_NAME
					$STATUS_SERVICE = $SERVICE.Status
					if ( $STATUS_SERVICE -eq "Running") {
					"O SERVIÇO ESTA EM EXECUÇÃO - STATUS ATUAL = $STATUS_SERVICE"
					"$DATE - O SERVIÇO ESTA EM EXECUÇÃO - STATUS ATUAL = $STATUS_SERVICE" >> $LOG | Out-Null
					} else {
					"O SERVIÇO NÃO INICIOU, FAVOR VERIFICAR - STATUS ATUAL = $STATUS_SERVICE"
					"$DATE - O SERVIÇO NÃO INICIOU, FAVOR VERIFICAR - STATUS ATUAL = $STATUS_SERVICE" >> $LOG | Out-Null
					exit
					}
					
					Write-Host "INICIANDO VALIDAÇÃO DA URL"
					sleep 10
					$req = [system.Net.WebRequest]::Create($URL)
					try {
					$res = $req.GetResponse()
					}
					catch [System.Net.WebException] {
					$res = $_.Exception.Response
					}
						if ( [int]$res.StatusCode -eq "200" -or $res.StatusCode -eq "301" -or $res.StatusCode -eq "401")
					{"VALIDAÇÃO DA URL: $URL REALIZADA COM SUCESSO"
					 "$DATE - VALIDAÇÃO DA URL: $URL REALIZADA COM SUCESSO" >> $LOG | Out-Null
					 }
						else {
					"VALIDAÇAO DA URL: $URL REALIZADA COM FALHA, VERIFIQUE O AMBIENTE" 
					"$DATE - VALIDAÇAO DA URL: $URL REALIZADA COM FALHA, VERIFIQUE O AMBIENTE" >> $LOG | Out-Null
					exit}
						
				} 
				else {
					"O PACOTE INFORMADO NAO EXISTE, POR FAVOR VERIFIQUE"
					"$DATE - O PACOTE INFORMADO NAO EXISTE, POR FAVOR VERIFIQUE" >> $LOG | Out-Null
					exit
				}
				'''
                }
			}
			
    }
}