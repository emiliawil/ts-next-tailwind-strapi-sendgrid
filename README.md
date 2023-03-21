# ts-next-tailwind-strapi-sendgrid
Template repo for projects using Typscript, Next.js, Tailwind CSS, Strapi Headless CMS and SendGrid.

I recommend using this stack (Typescript, Next.js, and Tailwind CSS) as it is modern, performant, and versatile. For a CMS solution, I suggest using Strapi because it's an open-source, self-hosted, headless CMS that offers a user-friendly interface for content management and is compatible with Next.js.

Here's a step-by-step process for generating the template repository:

Install the required tools:
Ensure that you have Node.js and npm installed on your system.

Create a new Next.js project:

```
npx create-next-app project-name --typescript
cd project-name
```

Install Tailwind CSS and its dependencies:

```
npm install -D tailwindcss@latest postcss@latest autoprefixer@latest
```

Create a tailwind.config.js file at the root of your project:

```
npx tailwindcss init
```

Create a postcss.config.js file at the root of your project with the following content:

```
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

Update your styles/globals.css to import Tailwind CSS:

```
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities';

Include the CSS file in your _app.tsx:

```
import '../styles/globals.css';
```

Install Strapi globally and create a new Strapi project:

```
npm install -g strapi@latest
strapi new cms --quickstart
```

Create content types for each client (e.g., PetPortrait, Portfolio, DogBreeding) and add relevant fields through the Strapi admin panel.

Configure API permissions for each content type in the Strapi admin panel under "Settings" > "Roles" > "Public". Set the permissions for "find" and "findone" to allow public access.

Install the necessary Next.js packages for fetching data from Strapi:

```
npm install axios
```

Create reusable components (e.g., Header, Footer, and ContactForm) and utility functions to fetch data from Strapi using Axios.

Use the fetched data to create dynamic pages for each client's content.

Here's a sample Axios utility function for fetching data from Strapi:

```
import axios from 'axios';

const apiClient = axios.create({
  baseURL: 'http://localhost:1337', // Replace with your Strapi instance URL
  withCredentials: false,
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/json',
  },
});

export default {
  async getPetPortraits() {
    const response = await apiClient.get('/pet-portraits');
    return response.data;
  },

  // Add more API functions for other content types
};
```

Feel free to customize the components, pages, and styling to match each client's requirements. This template repository will provide you with a solid foundation for building small business websites with a user-friendly CMS for content management.

For handling contact forms, I suggest using a serverless function to process the form submissions. This approach works well with the Next.js framework and can be easily integrated with various email service providers like SendGrid, Mailgun, or using SMTP directly. I'll outline the process using SendGrid.

Install the necessary packages:

```
npm install @sendgrid/mail
```

Sign up for a SendGrid account and obtain an API key.

Create a .env.local file in the root of your Next.js project and add the SendGrid API key:

```
SENDGRID_API_KEY=your_sendgrid_api_key
```

Create a new folder called pages/api if it doesn't exist, and create a file named sendEmail.ts inside it with the following content:


```
import type { NextApiRequest, NextApiResponse } from 'next';
import sgMail from '@sendgrid/mail';

sgMail.setApiKey(process.env.SENDGRID_API_KEY as string);

const sendEmail = async (req: NextApiRequest, res: NextApiResponse) => {
  if (req.method === 'POST') {
    const { name, email, message } = req.body;

    const msg = {
      to: 'recipient@example.com', // Replace with the email address where you want to receive messages
      from: 'sender@example.com', // Replace with your SendGrid email address or a verified sender
      subject: 'New message from your website',
      text: `Name: ${name}\nEmail: ${email}\nMessage: ${message}`,
      html: `
        <h3>New message from your website:</h3>
        <ul>
          <li>Name: ${name}</li>
          <li>Email: ${email}</li>
          <li>Message: ${message}</li>
        </ul>
      `,
    };

    try {
      await sgMail.send(msg);
      res.status(200).json({ message: 'Email sent successfully!' });
    } catch (error) {
      console.error(error);
      res.status(500).json({ message: 'An error occurred while sending the email.' });
    }
  } else {
    res.setHeader('Allow', 'POST');
    res.status(405).json({ message: `Method ${req.method} is not allowed.` });
  }
};

export default sendEmail;
```

Create a ContactForm component that submits the form data to the /api/sendEmail endpoint:

```
import { useState } from 'react';
import axios from 'axios';

const ContactForm = () => {
  const [formData, setFormData] = useState({ name: '', email: '', message: '' });

  const handleChange = (e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement>) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    try {
      await axios.post('/api/sendEmail', formData);
      alert('Message sent successfully!');
      setFormData({ name: '', email: '', message: '' });
    } catch (error) {
      console.error(error);
      alert('An error occurred while sending the message.');
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Render form fields and buttons */}
    </form>
  );
};

export default ContactForm;
```

Include the ContactForm component on the relevant pages or in a shared layout. This solution provides a simple, serverless contact form for your clients that can be easily customized to fit their needs.
