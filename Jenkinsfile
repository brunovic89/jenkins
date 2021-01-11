pipeline { 
    agent {label 'master'}
		parameters {
				string(name: 'URL',defaultValue: '', description: 'Digite o endereço da URL a ser testada')
				string(name: 'PACOTE', defaultValue: '', description: 'Digite o nome do pacote a ser deployado')
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
					$DATESTAMP = get-date -uformat "%Y%m%d@%H-%M-%S"
					$TESTE_PACOTE = Test-Path "C:\\Program Files\\Apache Software Foundation\\Tomcat 8.5\\webapps\\$FILE.war"
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
							Write-Host "TENTANDO PARA O SERVIÇO"
							"$DATE - TENTANDO PARA O SERVIÇO" >> $LOG | Out-Null
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
					
						
					
						Write-Host "FAZENDO O BACKUP DO PACOTE"
						"$DATE - FAZENDO O BACKUP DO PACOTE" >> $LOG | Out-Null
						Copy-Item "$APP_DIR\\$FILE.war" -Destination "$BKP_DIR\\$FILE.war_$DATESTAMP" 
						Remove-Item "$APP_DIR\\$FILE.war", "$APP_DIR\\$FILE" -Recurse
					
						
						Write-Host "COPIANDO NOVO PACOTE"
						"$DATE - COPIANDO NOVO PACOTE" >> $LOG | Out-Null
						Copy-Item "$DEPLOY_APP\\$FILE.war" -Destination "$APP_DIR"
					
						
						Write-Host "INICIANDO O TOMCAT"
						"$DATE - INICIANDO O TOMCAT" >> $LOG | Out-Null
						Start-Service -Name $SERVICE_NAME
						Start-Sleep 10
						
						$SERVICE = Get-Service $SERVICE_NAME
						$STATUS_SERVICE = $SERVICE.Status
						Write-Host "O SERVIÇO ESTA $STATUS_SERVICE" 
						"$DATE - O SERVIÇO ESTA $STATUS_SERVICE" >> $LOG | Out-Null
					
							
					} 
					else {
						"NÃO EXISTE O PACOTE, SAINDO DO SCRIPT"
						pause
					}
					'''
                }
			}
			stage('TESTE DE APLICAÇÃO'){ 
			   steps{
		        echo "SCRIPT PARA TESTE DE URL DA APLICAÇÃO"
                powershell '''
					  $url = $env:URL
                      $req = [system.Net.WebRequest]::Create($url)
                      
                      try {
                          $res = $req.GetResponse()
                      }
                      catch [System.Net.WebException] {
                          $res = $_.Exception.Response
                      }
                      if ( [int]$res.StatusCode -eq "200" -or $res.StatusCode -eq "301" -or $res.StatusCode -eq "401")
                       {"validação da $url realizada com sucesso"}
                       else {
                       "Falha na validação da $url, por favor verifique o ambiente "}
					   '''
					}
				}
    }
}