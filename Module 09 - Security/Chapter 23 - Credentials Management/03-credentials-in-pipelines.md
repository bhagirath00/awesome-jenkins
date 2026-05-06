# Credentials in Pipelines: Unlocking the Vault

## 1. Introduction: The Magic Safe and the Stage Show

Imagine you are a **World-Famous Magician**.
You are performing a massive show on a grand stage.
One of your tricks involves a **Magic Safe**.
The safe is sitting on the stage, and everyone can see it.
Inside the safe, there is a "Secret Scroll."

You don't walk onto the stage and yell the "Combination" to the
audience.
That would ruin the trick and reveal the secret to everyone.
Instead, you walk over to the safe, you hide your hands with a
silk cloth (the **Masking**), and you whisper the combination
to the lock.
The safe opens.
You pull out the scroll, you show the audience the "Information" they
need to see, and then you put the scroll back and lock the safe.
The audience saw the "Result" of the secret, but they never saw
the "Secret" itself.

In the world of Jenkins, **Credentials in Pipelines** is that magic
trick.
It is the process of bringing a secret from the high-security vault
onto the "Stage" of your build, using it for a specific task (like
logging into AWS), and then putting it back before the logs are
shown to the world.

## 2. The `credentials()` Helper: The Senior Shortcut

In a Declarative Pipeline, the most common way to "Open the Safe"
is to use the `credentials()` function inside an `environment` block.

```groovy
pipeline {
    agent any
    environment {
        // We are "Mapping" the secret to an environment variable
        MY_SECRET_KEY = credentials('aws-prod-key-id')
    }
    stages {
        stage('Deploy') {
            steps {
                // We use the variable, not the secret ID
                sh "aws s3 sync ./dist s3://my-bucket --secret-key=${env.MY_SECRET_KEY}"
            }
        }
    }
}
```

### What happens under the hood?
Jenkins "Fetches" the secret from the vault.
It "Masks" the secret so that if you accidentally try to `echo` it,
the logs will only show `****`.
This is your **Safety Guardrail**.

---

## 3. The `withCredentials` Block: Precision Control

Sometimes, you only need the secret for one specific step.
A senior architect uses the `withCredentials` block for **Precision**.

```groovy
stage('Login') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'docker-hub', 
                                         passwordVariable: 'PASS', 
                                         usernameVariable: 'USER')]) {
            sh "docker login -u ${USER} -p ${PASS}"
        }
    }
}
```

This is even safer than the environment block because the secret
is only "Unwrapped" and available inside that one specific block
of code.
As soon as the `}` is reached, the secret is "Locked" back in the vault.

---

## Summary

- The credentials() helper is the standard way to inject secrets into pipeline environments
- Jenkins automatically "masks" secrets in the console logs, replacing them with asterisks
- Using environment variables to hold secrets prevents their accidental exposure in code
- The withCredentials block provides precision control by limiting the secret's availability
- Masking acts as a critical safety guardrail against accidental disclosure to developers
- Secrets are only "unwrapped" during the execution phase and remain encrypted at rest
- Mastering credential injection is the key to building secure and automated AWS deployments

In the next file, `04-credentials-best-practices.md`, you will learn the senior-level rules for maintaining a professional vault.
