pipeline {
  agent any

  stages {
    stage('Clone') {
      steps {
        git branch: 'main', url: 'https://github.com/Qthanh074/tkpm1.git'
      }
    }

    stage('Restore & Build') {
      steps {
        echo '🛠️ Restore và build bằng MSBuild…'
        bat '''
        "C:\\Program Files\\Microsoft Visual Studio\\2022\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe" SNKRS.csproj /t:Restore,Build /p:Configuration=Release
        '''
      }
    }

    stage('Tests') {
      steps {
        echo '🏃‍♂️ Chạy tests…'
        bat 'dotnet test --no-build --verbosity normal'
      }
    }

    stage('Publish') {
      steps {
        echo '📦 Publish…'
        bat 'dotnet publish -c Release -o "%WORKSPACE%\\publish"'
      }
    }

    stage('Deploy to IIS') {
      steps {
        echo '🚀 Deploy lên IIS…'
        bat 'iisreset /stop'
        bat '''
        if exist "%WORKSPACE%\\publish" (
          rmdir /S /Q "C:\\inetpub\\wwwroot\\TrienKhaiPhamMem"
          mkdir "C:\\inetpub\\wwwroot\\TrienKhaiPhamMem"
          xcopy "%WORKSPACE%\\publish" "C:\\inetpub\\wwwroot\\TrienKhaiPhamMem" /E /Y /I /R
        ) else (
          echo Publish folder not found!
          exit /b 1
        )
        '''
        bat 'iisreset /start'
        powershell '''
          Import-Module WebAdministration
          if (-not (Test-Path IIS:\\Sites\\TKPM4)) {
            New-Website -Name "TKPM4" -Port 83 -PhysicalPath "C:\\inetpub\\wwwroot\\TrienKhaiPhamMem" -Force
          }
        '''
      }
    }
  }
}
