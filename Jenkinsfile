#!groovy

node {
	step([$class: 'StashNotifier'])
	try {
		stage("Clone") {
			checkout scm
		}

		stage("Restore") {
			bat "dotnet restore --source https://packages/repository/nuget-group-libs/"
		}

		stage("Build") {
			bat("\"${tool 'MSBuild15'}\" Winton.AspNetCore.Seo.sln /t:Jenkins /p:Platform=\"Any CPU\" /p:Configuration=Release")
		}

		stage("Test") {
			dir("test\\Winton.AspNetCore.Seo.Tests") {
				bat("dotnet test Winton.AspNetCore.Seo.Tests.csproj --configuration Release --no-build")
			}
		}

		stage("Publish") {
			dir("src\\Winton.AspNetCore.Seo\\bin") {
				bat("dotnet nuget push **\\*.nupkg --source https://packages/repository/nuget-hosted-libs/")
			}
		}

		stage("Archive") {
			archive "**\\*.nupkg"
		}

		currentBuild.result = "SUCCESS"
	}
	catch (err) {
		currentBuild.result = "FAILURE"
		throw err
	}
	finally {
		step([$class: 'StashNotifier'])
	}
}