---
tags: [template, spec-prompt, principled-ai-coding]
date: 2025-12-09
source: Agentic Engineering methodology
usage: Replace vibe coding with structured specifications
---

# Spec Prompt Template

**Purpose**: Transform "vibe coding" into structured, 1000+ token specifications for reliable AI coding.

## Template Structure

```markdown
# [Feature/Task Name]

## Context
[2-3 paragraphs explaining the current state, why this is needed, and how it fits into the larger system]

## Objectives
1. [Primary objective - what success looks like]
2. [Secondary objectives]
3. [Tertiary objectives]

## Constraints
- **Technical**: [Framework versions, APIs, dependencies]
- **Performance**: [Speed, memory, scalability requirements]
- **Security**: [Authentication, authorization, data protection]
- **Compatibility**: [Browser support, backwards compatibility]
- **Style**: [Code conventions, patterns to follow]

## Current State Analysis
### Existing Code
[Describe relevant existing code/patterns]

### Pain Points
[What's currently broken or suboptimal]

### Dependencies
[Files, modules, services this will interact with]

## Implementation Strategy

### Approach
[Chosen approach and why - if multiple options, explain trade-offs]

### Architecture
```
[ASCII diagram or description of component relationships]
```

### Key Components
1. **[Component 1]**
   - Purpose: [What it does]
   - Location: [File path]
   - Interface: [Key methods/props]

2. **[Component 2]**
   - Purpose: [What it does]
   - Location: [File path]
   - Interface: [Key methods/props]

### File Changes
- `path/to/file1.ts` - [What changes]
- `path/to/file2.ts` - [What changes]
- `path/to/new-file.ts` - [New file, purpose]

## Detailed Implementation Steps

### Phase 1: [Name]
1. [Specific step with exact file paths]
2. [Specific step with code examples]
3. [Specific step with expected outcome]

### Phase 2: [Name]
1. [Specific step]
2. [Specific step]

### Phase 3: [Name]
1. [Specific step]
2. [Specific step]

## Verification Steps

### Unit Tests
```typescript
// Example test structure
describe('[Feature]', () => {
  it('should [behavior]', () => {
    // Test implementation
  });
});
```

### Integration Tests
[How to verify components work together]

### Manual Testing
1. [Step-by-step manual test]
2. [Expected result]

### Success Criteria
- [ ] All unit tests pass
- [ ] Integration tests pass
- [ ] Manual test completes successfully
- [ ] No performance regression
- [ ] Code review approved

## Edge Cases & Error Handling

### Edge Cases
1. **[Edge case 1]**: [How to handle]
2. **[Edge case 2]**: [How to handle]

### Error Scenarios
1. **[Error scenario 1]**: [Expected behavior]
2. **[Error scenario 2]**: [Expected behavior]

## Rollback Plan
[If something goes wrong, how to revert]

## Future Considerations
[What might need to change later, technical debt acknowledged]

## References
- [Link to relevant docs]
- [Link to similar patterns in codebase]
- [Link to discussion/ticket]
```

---

## Example: User Authentication Feature

```markdown
# User Authentication with JWT

## Context
Current application has no authentication. Users access all features publicly. We need to add user accounts and protect sensitive routes. The app uses Express backend with React frontend. We'll implement JWT-based auth for stateless authentication that scales.

## Objectives
1. Enable user registration and login
2. Protect API endpoints requiring authentication
3. Store user sessions securely without server-side state
4. Provide smooth UX for auth failures

## Constraints
- **Technical**: Express 4.x, React 18, bcrypt for hashing, jsonwebtoken library
- **Performance**: Token validation < 10ms, password hashing non-blocking
- **Security**: Passwords hashed with bcrypt (10 rounds), JWT signed with RS256, tokens expire in 24h
- **Compatibility**: Tokens work across subdomains
- **Style**: Follow existing Express middleware pattern, React hooks for auth state

## Current State Analysis

### Existing Code
- Express server in `src/server/index.ts`
- API routes in `src/server/routes/`
- React app in `src/client/`
- No user database yet

### Pain Points
- Anyone can access admin features
- No user tracking for analytics
- Can't personalize experience

### Dependencies
- Will need: PostgreSQL for users, middleware for auth, React context for client auth state
- Affects: All protected API routes, client routing

## Implementation Strategy

### Approach
JWT with refresh tokens. Chose over sessions because:
- Stateless (no Redis needed)
- Works across multiple servers
- Client can check expiry without server call

Trade-off: Can't revoke tokens instantly (mitigated with short expiry)

### Architecture
```
Client                Server                Database
  |                     |                      |
  |-- POST /register -->|                      |
  |                     |-- hash password ---->|
  |                     |<---- save user ------|
  |<--- JWT token ------|                      |
  |                     |                      |
  |-- API request ----->|                      |
  |   (JWT in header)   |                      |
  |                     |-- verify JWT         |
  |                     |-- if valid, process  |
  |<--- response -------|                      |
```

### Key Components
1. **Auth Middleware**
   - Purpose: Validate JWT on protected routes
   - Location: `src/server/middleware/auth.ts`
   - Interface: `authenticateToken(req, res, next)`

2. **User Model**
   - Purpose: User CRUD operations
   - Location: `src/server/models/User.ts`
   - Interface: `create()`, `findByEmail()`, `verifyPassword()`

3. **Auth Routes**
   - Purpose: Handle login/register/refresh
   - Location: `src/server/routes/auth.ts`
   - Interface: POST `/register`, `/login`, `/refresh`

4. **Auth Context (Client)**
   - Purpose: Global auth state in React
   - Location: `src/client/contexts/AuthContext.tsx`
   - Interface: `useAuth()` hook

### File Changes
- `src/server/models/User.ts` - New user model
- `src/server/middleware/auth.ts` - New JWT middleware
- `src/server/routes/auth.ts` - New auth routes
- `src/server/index.ts` - Register auth routes
- `src/client/contexts/AuthContext.tsx` - New auth context
- `src/client/App.tsx` - Wrap with AuthProvider
- `src/client/api/client.ts` - Add JWT to requests

## Detailed Implementation Steps

### Phase 1: Database & Model
1. Create migration: `migrations/001_create_users_table.sql`
   ```sql
   CREATE TABLE users (
     id SERIAL PRIMARY KEY,
     email VARCHAR(255) UNIQUE NOT NULL,
     password_hash VARCHAR(255) NOT NULL,
     created_at TIMESTAMP DEFAULT NOW()
   );
   ```
2. Run migration: `npm run migrate`
3. Create `src/server/models/User.ts`:
   ```typescript
   export class User {
     static async create(email: string, password: string): Promise<User>
     static async findByEmail(email: string): Promise<User | null>
     async verifyPassword(password: string): Promise<boolean>
   }
   ```

### Phase 2: Auth Routes & Middleware
1. Create `src/server/middleware/auth.ts`:
   ```typescript
   export const authenticateToken = (req, res, next) => {
     const token = req.headers.authorization?.split(' ')[1];
     if (!token) return res.sendStatus(401);

     jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
       if (err) return res.sendStatus(403);
       req.user = user;
       next();
     });
   };
   ```

2. Create `src/server/routes/auth.ts`:
   ```typescript
   router.post('/register', async (req, res) => {
     const { email, password } = req.body;
     const user = await User.create(email, password);
     const token = jwt.sign({ id: user.id }, process.env.JWT_SECRET);
     res.json({ token });
   });
   ```

3. Protect existing routes:
   ```typescript
   // In routes that need auth
   router.get('/protected', authenticateToken, (req, res) => {
     res.json({ data: 'sensitive' });
   });
   ```

### Phase 3: Client Integration
1. Create `src/client/contexts/AuthContext.tsx`:
   ```typescript
   export const AuthContext = createContext<AuthContextType>(null);

   export function AuthProvider({ children }) {
     const [token, setToken] = useState(localStorage.getItem('token'));

     const login = async (email, password) => {
       const res = await fetch('/api/auth/login', {
         method: 'POST',
         body: JSON.stringify({ email, password })
       });
       const { token } = await res.json();
       localStorage.setItem('token', token);
       setToken(token);
     };

     return (
       <AuthContext.Provider value={{ token, login, logout }}>
         {children}
       </AuthContext.Provider>
     );
   }
   ```

2. Add to requests in `src/client/api/client.ts`:
   ```typescript
   const token = localStorage.getItem('token');
   headers: {
     'Authorization': `Bearer ${token}`
   }
   ```

## Verification Steps

### Unit Tests
```typescript
describe('Auth Middleware', () => {
  it('should reject requests without token', async () => {
    const res = await request(app).get('/protected');
    expect(res.status).toBe(401);
  });

  it('should accept valid token', async () => {
    const token = jwt.sign({ id: 1 }, process.env.JWT_SECRET);
    const res = await request(app)
      .get('/protected')
      .set('Authorization', `Bearer ${token}`);
    expect(res.status).toBe(200);
  });
});
```

### Integration Tests
1. Register new user → Returns token
2. Use token in protected route → Success
3. Use expired token → 403
4. Login with wrong password → 401

### Manual Testing
1. Open app → See login page
2. Register with email/password → Redirected to dashboard
3. Refresh page → Still logged in
4. Logout → Back to login page
5. Try accessing protected route → Redirected to login

### Success Criteria
- [ ] All unit tests pass (10+ tests)
- [ ] Integration tests pass
- [ ] Manual test flow completes
- [ ] Password hashing takes < 100ms
- [ ] JWT validation < 10ms
- [ ] Code review approved
- [ ] No sensitive data in logs

## Edge Cases & Error Handling

### Edge Cases
1. **User registers with existing email**: Return 409 Conflict, clear error message
2. **Token expires during request**: Return 403, client refreshes token automatically
3. **Malformed JWT**: Return 400 Bad Request
4. **Database connection fails during login**: Return 503 Service Unavailable

### Error Scenarios
1. **Network failure during registration**: Show retry button, don't lose form data
2. **Token refresh fails**: Log out user, redirect to login
3. **Concurrent login attempts**: Last login wins, invalidate previous tokens

## Rollback Plan
1. Remove auth routes from `src/server/index.ts`
2. Remove auth middleware from protected routes
3. Optionally: Keep user table for future attempt

## Future Considerations
- Add refresh tokens for better security
- Implement OAuth for social login
- Add 2FA for sensitive accounts
- Rate limiting on auth endpoints
- Password reset flow

## References
- JWT Best Practices: https://tools.ietf.org/html/rfc8725
- bcrypt Security: https://github.com/kelektiv/node.bcrypt.js#security-issues-and-concerns
- Existing middleware pattern: `src/server/middleware/logger.ts`
```

---

## Anti-Pattern Examples

### ❌ Vibe Coding (Don't do this)
```
User: "Add authentication"
AI: "I'll add JWT auth..."
[Proceeds to write code without full context]
```

### ✅ Spec Prompt (Do this)
```
User: "Add authentication"
AI: "Let me create a spec first..."
[Creates 1000+ token spec with full context, constraints, implementation steps]
User: Reviews and approves spec
AI: Implements exactly per spec
```

---

## Usage Tips

1. **When to use**: Any task beyond trivial bug fixes
2. **Time investment**: 5-10 minutes writing spec saves hours debugging
3. **Collaboration**: Share spec for review before implementation
4. **Iteration**: Spec evolves - update it as you learn
5. **Archive**: Save specs in `/docs/specs/` for future reference

---

**Remember**: "Vibe coding" = hoping for magic. Spec prompts = engineered reliability.
