# SA (Paprika web) - FE Onboarding Process

## Prerequisites

1. node version: 18, npm version: 9.8.*
2. Permission to access [https://github.com/Rakuten-MTSD-PAIS/paprika_web](https://github.com/Rakuten-MTSD-PAIS/paprika_web)
3. Permission to access [https://github.com/Rakuten-MTSD-PAIS/paprika_log_system](https://github.com/Rakuten-MTSD-PAIS/paprika_log_system) (Read Permissions)
4. ec2_key.pem file located at ~/.ssh/ec2_key.pem
    1. Download and move **ec2_key.pem** to **~/.ssh/ec2_key.pem**
        1. Ask for **ec2_key.pem** file (ask to someone in charge)
        2. Grant **600 permission** to ec2_key.pem file
            - `chmod 600 ~/.ssh/ec2_key.pem`

## Installation

1. Clone the **paprika_web repository**
2. Execute **npm install** at the root directory of the project
3. execute **npm run develop** OR **npm run sshTunneling + npm run watch:local + npm run dev**

## Sign Up & Verify Email

1. **Signup** using a test account on either localhost or [http://test.send-anywhere.com](http://test.send-anywhere.com/)
    1. Test account must end on **@eoq.kr**,
    2. Pick a random username yourself
    3. Set a password
2. Visit https://[test.send-anywhere.com/api/v1/verify/email?email=${YOUR_EMAIL_ADDRESS}](http://test.send-anywhere.com/api/v1/verify/email?email=${YOUR_EMAIL_ADDRESS}) to **get your email address verified in the test server**
    - *Replace ${YOUR_EMAIL_ADDRESS} with your email address you used to register*

## Purchase a Subscription

Visit [test.send-anywhere.com](http://test.send-anywhere.com/), login and proceed with applying for a paid subscription plan

1. Go to **[Pricing](https://test.send-anywhere.com/pricing)** tab from menu bar above
2. Click **Get Started** button of the desired plan.
3. Enter your date of birth, check ‘18 years or older’, agree to Terms of Service and click **Payment**
4. Fill out **4242-4242-4242-4242 (card number), 04/24 (MM/YY), 424 (CVC)**
5. Enter your name and click Subscribe button.
6. Watch the attached video below for details
